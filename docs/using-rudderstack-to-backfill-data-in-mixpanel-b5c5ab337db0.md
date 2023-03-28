# 使用 RudderStack 在 Mixpanel 中回填数据

> 原文：<https://medium.com/codex/using-rudderstack-to-backfill-data-in-mixpanel-b5c5ab337db0?source=collection_archive---------11----------------------->

![](img/ddc347ca4b1006fe88e7a96fbf7db841.png)

我们最近[宣布了我们与 Mixpanel](https://rudderstack.com/blog/rudderstack-and-mixpanel-announce-partnership-advancing-product-analytics-for-the-modern-data/)的合作关系，在过去的几个月里，由于[将 RudderStack 连接到 Mixpanel](https://rudderstack.com/docs/destinations/analytics/mixpanel/#mixpanel) ，我们一直在忙着构建仪表盘来驱动洞察力。不幸的是，我们的分析仅限于从我们开始提交数据的时间范围。为了从更早的开始日期进行报告，我们需要一种方法将之前处理过的 RudderStack 数据加载到 Mixpanel 中。

为此，我们探索了两种选择:

*   重放我们的 S3 桶中的所有事件，并将它们发送到 Mixpanel
*   创建一个舵栈[仓库动作](https://rudderstack.com/product/warehouse-actions/)来重放已经存储在我们雪花仓库中的数据

第一个选项，重放我们的 S3 桶中的事件并将它们发送到 Mixpanel，是最明显的。有了一个新的 user [转换](https://rudderstack.com/docs/transformations/)来更新这个回填源的时间戳，我们认为很快就可以启动并运行了。总的来说，这种方法很有意义，但是在过去的六个月中，我们对我们的堆栈做了相当大的改变，并且在实现我们的跟踪计划时，我们改变了我们的命名约定。因此，这个选项意味着我们将在 Mixpanel 中创建一堆额外的用户配置文件和事件属性，这些在我们的 RudderStack 应用程序中已经被否决了。我们可以在用户转换中清除这种干扰，但是我们认为在数据仓库中这样做可能更容易。

因此，我们选择了第二个选项:创建一个 RudderStack Warehouse 操作来重放已经存储在雪花仓库中的数据。通过利用 Snowflake，我们可以在将数据加载到生产环境之前，清理、准备和测试数据，并将其加载到 Mixpanel 的临时项目中。这种方法还允许我们规范化数据模式，并将我们希望加载的属性数量减少到那些对我们特定的 Mixpanel 用例重要的属性。是的，类似于 S3 选项，这可以通过用户转换来完成，但是我们认为清理数据和规范化数据库中的模式会更容易。我们是这样做的:

# 步骤 1:确定正确的途径，识别并呼叫

如果您是 RudderStack 的新手，您可能不熟悉 RudderStack 创建的用于在数据仓库中存储不同事件类型和跟踪调用的模式[。对于我们的用例，我们主要关注转换前和转换后的用户转换活动，所以我们想处理与页面一起的预先识别呼叫，并跟踪来自我们网站的呼叫。所有这些先前的事件都将与一个事件类型列一起加载到一个“重放”表中，该列将用于仓库操作的用户转换。如果我们只需要重放我们的页面调用，我们可以在雪花中的 page_view 表中使用仓库操作，但是因为我们有超过 12 种不同的事件类型，所以我们决定将所有事件合并到一个重放中，并且只运行仓库操作一次，而不是运行多个仓库操作，雪花中的每个事件类型表一个仓库操作。](https://rudderstack.com/docs/data-warehouse-integrations/warehouse-schemas/)

# 第 2 步:在雪花中创建临时表

如上所述，我们希望从不同的页面重放事件，跟踪和识别表，每个表都有比我们需要的更多的列。我们决定将各种表合并成一个重放表，作为单个仓库操作的来源。这也允许我们重命名事件和属性，这些事件和属性是从更早的时候就已经改变了。如果我们没有重命名新表中的列，我们仍然可以在 Mixpanel 中创建公式来组合各种属性，但是用雪花和/或用户转换来清理要容易得多。

# 查看 Mixpanel 词典

在创建新表时，查看我们的生产实例中的 Mixpanel 词典以查看事件和用户配置文件属性很有帮助。*需要注意的是，RudderStack 和 Mixpanel 都是区分大小写的，因为雪花中的 RudderStack 模式是用所有大写字母创建的，所以这些列需要在用户转换中进行修改。*

# 第 3 步:定义用户转换映射

RudderStack 仓库操作将从我们的重放表中加载每一行，作为一个标识调用，每一列作为一个特征。因此，我们创建了一个用户转换，以在必要时将事件类型更改为页面或跟踪，并将特征移动到适当的属性或上下文对象。我们还将电子邮件地址映射到 userId 属性，并将时间戳显式设置为原始时间戳值(否则 Rudderstack 目标转换器将使用事件的当前时间戳)。

我们还添加了一个名为“逆火日期”的新属性，以便在需要时可以很容易地在 Mixpanel 中过滤掉这些事件。

```
*export* *function* transformEvent(event) {event.timestamp = event.traits.TIMESTAMPevent.userId = event.traits.CONTEXT_TRAITS_EMAILevent.new_event = event.traits.EVENT*if* (event.type == 'identify')  {event.context["traits"];event.context["page"];*let* page = {initial_referrer : event.CONTEXT_PAGE_INITIAL_REFERRER,initial_referring_domain : event.CONTEXT_PAGE_INITIAL_REFERRING_DOMAIN,path : event.CONTEXT_PAGE_PATH,referrer : event.CONTEXT_PAGE_REFERRER,search : event.CONTEXT_PAGE_SEARCH,title : event.CONTEXT_PAGE_TITLE,url : event.CONTEXT_PAGE_URL}event.context = {page : page}*let* traits = {account_type : event.traits.CONTEXT_TRAITS_ACCOUNT_TYPE,utm_campaign : event.traits.CONTEXT_TRAITS_UTM_CAMPAIGN,utm_content : event.traits.CONTEXT_TRAITS_UTM_CONTENT,utm_medium : event.traits.CONTEXT_TRAITS_UTM_MEDIUM,utm_source : event.traits.CONTEXT_TRAITS_UTM_SOURCE,utm_term : event.traits.CONTEXT_TRAITS_UTM_TERM,}event.context = {traits : traits}// Mixpanel Mapped Fieldsevent.context.traits.email = event.traits.CONTEXT_TRAITS_EMAILevent.context.traits.firstName = event.traits.CONTEXT_TRAITS_FIRST_NAMEevent.context.traits.lastName = event.traits.CONTEXT_TRAITS_LAST_NAMEevent.context.traits.backfill_date = "2021-11-07T12:00:00.00Z"}*if* ((event.new_event == 'form_submit'  || event.new_event == 'webhook_source_event' || event.new_event == 'user_signed_up'))  {event.type = 'track';event.event = event.traits.EVENT_TEXTevent.name = event.EVENT_TEXTevent.properties.form_id = event.context.traits.form_id}*if* (event.new_event == 'page')  {event.type = 'page';event.event = "page_view"event.name = "page_view"}*delete* event.traits;*return* event}
```

# 第 4 步:在 Mixpanel 中设置测试环境

如果可能的话，总是建议在将数据加载到产品中之前，在 Mixpanel 中创建一个新项目来测试您的数据重放。

快速提示:Mixpanel 下拉菜单只显示最近 30 天内的事件及其属性。这最初让我们感到困惑，因为我们可以在 eventstream 窗口中看到事件(当我们将时间范围向后设置几个月时)，但当我们在 report builder 中对数据进行 QA 时看不到它们。为了填充这些菜单，我们必须提交一个样本页面，并使用当前时间戳跟踪呼叫。

# 步骤 5:创建仓库操作并发送测试事件

有了 replay 表中暂存的数据和创建的用户转换，最后一步是实际创建 RudderStack Warehouse 动作，将重放事件流式传输到 Mixpanel。我们使用了完全同步选项，然后在初始同步后禁用了数据源，因为我们只需要向测试环境发送一次数据(查看我们的[雪花仓库操作数据源文档](https://rudderstack.com/docs/warehouse-actions/snowflake/)，了解我们如何连接重播表的具体信息)。一旦 QA 完成，我们只需更新 Mixpanel 目标 API 密钥和生产过程的秘密，并重新运行完全同步。

随着历史数据加载到产品中，我们能够从 Mixpanel 中全年的数据中获得洞察力，这让我们的销售和营销团队非常高兴。

# 你自己试试吧

如果你想了解更多关于用方向舵堆栈在 Mixpanel 中回填数据的信息，[免费注册](https://app.rudderlabs.com/signup?type=freetrial)，查看我们的 [Mixpanel 文档](https://rudderstack.com/docs/destinations/analytics/mixpanel/#sending-historic-events)中的更多细节，如果你有任何问题[请给我们发一封短信](https://rudderstack.com/contact/)。

*本帖最初发布于:* [*https://rudder stack . com/blog/using-rudder stack-to-back fill-data-in-mixpanel*](https://rudderstack.com/blog/using-rudderstack-to-backfill-data-in-mixpanel)