# 3.0.0 Alpha 版本！九个新特性和一个全新的用户界面为日程安排系统提供了新的功能

> 原文：<https://medium.com/codex/3-0-0-alpha-release-93ef625fae0e?source=collection_archive---------14----------------------->

![](img/a3744a8e5276bee0457452bebec9f4e8.png)

2022 年 4 月 22 日，Apache DolphinScheduler 正式宣布发布 alpha 版本 3.0.0！此次版本升级迎来了自发布以来最大的变化，许多新的特性和功能为用户带来了新的体验和价值。

简而言之，3.0.0-alpha 的关键词是“更快、更现代、更强大、更易于维护”。

*   更快、更现代:重新设计的 UI 具有新的界面，不仅用户响应速度提高了数十倍，开发人员构建速度提高了数百倍，而且页面布局和图标风格也更加现代。
*   更强大:带来了许多令人兴奋的新功能，如数据质量检查，自定义时区，支持 AWS，以及添加多个任务插件和多个警报插件。
*   更容易维护:后端服务分离更符合容器化和微服务的趋势，并且通过明确每个服务的责任，使维护变得更容易。

# 新特性和功能

## 01 全新 UI，更强大、更快速的前端代码

3.0.0-alpha 最大的变化是引入了一个新的 UI，它消除了切换语言时重新加载页面的需要，并且增加了一个黑暗主题。新的 UI 使用了 Vue3、TSX 和 Vite 相关的技术栈。与早期的 UI 相比，新的 UI 不仅更加现代和用户友好，而且前端也更加健壮，允许用户在发现代码中的问题时检查接口参数，从而产生更加健壮的前端代码。

此外，新的架构和技术堆栈不仅可以让用户操作 Apache DolphinScheduler 的响应速度提高数十倍，而且开发人员在本地编译和启动 UI 的速度也将提高数百倍，这将大大减少开发人员调试和打包代码的时间。

体验全新的用户界面:

![](img/2b22f7728a1d0a6169d9c6763638b2d3.png)![](img/550ca9918b0ee7ec7b000fa586dabae0.png)

本地启动时间比较

![](img/c86f1bb43107360012eea20570870ab9.png)

主页

![](img/f39b05c209c7d0ba18bdd2b56ea76781.png)

工作流实例

![](img/ea7bf4a7a472c5dcc4ef5305674ad359.png)

外壳任务页面

![](img/f816ecd8d6d5853547d53dab6dfe5d13.png)

MySQL 数据源页面

## 02 对 AWS 的支持

随着 Apache DolphinScheduler 用户的增长，它吸引了许多海外用户。然而，在研究过程中，用户发现有两个瓶颈影响了他们对 Apache DolphinScheduler 的体验，一个是时区问题，另一个是缺乏对海外云厂商的支持，尤其是 AWS。出于这个原因，我们决定支持 AWS 的重要组件，这是这个版本中最重要的变化之一。

Apache DolphinScheduler 现在支持 Amazon EMR 和 Amazon Redshift 任务类型的 AWS，并实现了对 Amazon S3 存储的资源中心支持。

*   对于 **Amazon EMR** ，我们已经创建了一个新的任务类型，并提供了其运行作业流特性，允许用户向 Amazon EMR 提交多个步骤的作业，并指定要使用的资源数量。详情可登陆:[https://dolphin scheduler . Apache . org/zh-cn/docs/latest/user _ doc/guide/task/EMR . html](https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/guide/task/emr.html?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE)

![](img/5bed09e8ff158daa283f50a5389eba51.png)

Amazon EMR 任务定义

*   对于 Amazon 红移，我们现在在 SQL 任务类型中扩展了对 Amazon 红移数据源的支持，用户现在可以通过在 SQL 任务中选择红移数据源来运行 Amazon 红移任务。

![](img/4536dbe7c865dd6f029c0bc26bd693bd.png)

亚马逊红移支持

*   对于**亚马逊 S3** ，我们扩展了 Apache DolphinScheduler 的资源中心，不仅支持本地资源、HDFS 资源存储，还支持亚马逊 S3 作为存储资源中心。详情请见:[https://dolphin scheduler . Apache . org/zh-cn/docs/latest/user _ doc/guide/resource . html](https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/guide/resource.html?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE)in

**`resource.storage.type`**

我们将在用户需要时支持更多的 AWS 任务，敬请关注。

## 03 服务分离

虽然新的 UI 是对 3.0.0-alpha 前端最大的改变，但对后端最大的改变是服务的分离。鉴于围绕容器和微服务的讨论越来越多，Apache DolphinScheduler 开发人员决定对后端服务进行分类。根据功能，我们将服务分为以下几个部分。

*   主服务器:主服务
*   工作服务器:工作服务
*   api-服务器:API 服务
*   警报服务器:警报服务
*   独立服务器:独立服务器，快速体验 Apache DolphinScheduler 功能
*   用户界面:用户界面资源
*   bin:快速启动脚本，主要是启动单个服务的脚本
*   工具:工具相关的脚本，主要是数据库创建和脚本更新

通过执行以下命令，可以启动或停止所有服务。

纯文本

` bin/dolphin scheduler-daemon . sh<start><server-name>'</server-name></start>

## 04 数据质量验证

在此版本中，期待已久的数据质量验证上线，解决了数据质量问题，如从源同步的数据项数量的准确性，以及每周或每月波动超过阈值的单个或多个表的警报。Apache DolphinScheduler 的早期版本解决了以特定的顺序和时间运行任务的问题，但它缺乏运行后常用的数据质量度量，这给用户带来了额外的开发成本。

现在，3.0.0-alpha 版中已经提供了对数据质量验证的原生支持，支持在工作流运行之前进行数据质量检查，从而在任务运行期间，通过数据质量功能模块中用户自定义的数据质量检查规则，实现对数据质量的严格控制和对结果的监控。

![](img/57be81bf60eef8e39df508fa6e8395ab.png)![](img/ed01bfd4a9c4c7788661254801e33bd0.png)

## 05 任务组

任务组用于控制任务实例的并发性，并定义组的优先级。当创建新的任务定义时，用户可以配置当前任务对应的任务组，并配置任务在任务组内运行的优先级。当一个任务被配置了一个任务组时，该任务只有在满足所有上游任务运行成功，并且任务组中当前运行的任务小于资源池的情况下才能执行。当它大于或等于资源池的大小时，任务将等到下一次检查。当一个任务组中的多个任务同时处于挂起队列中时，优先级最高的任务将首先运行。

详见链接:[https://dolphin scheduler . Apache . org/zh-cn/docs/3 . 0 . 0/user _ doc/guide/resource . html](https://dolphinscheduler.apache.org/zh-cn/docs/3.0.0/user_doc/guide/resource.html?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE)

![](img/c93573fc2245856b0d9f0aeac733c6b0.png)

## 06 自定义时区

在 3.0.0-alpha 之前的版本中，Apache DolphinScheduler 默认为 UTC+8 时区，但随着用户群的扩大，海外用户和在海外跨时区做生意的人往往会被时区搞糊涂。3.0.0-alpha 支持时区切换，为海外用户解决了时区问题。例如，如果一个企业业务涉及东 8 和西 5 的时区，如果要使用同一个 DolphinScheduler 集群，可以创建多个用户，每个用户将使用自己的本地时区，相应的 DolphinScheduler 对象中显示的时间将切换到相应时区的本地时间，这更符合本地开发者的使用习惯。对应的 DolphinScheduler 对象中显示的时间会切换到当地时区，更符合当地开发者的使用习惯。

![](img/a2540f448950bc626f48cc3f2ce44415.png)

见链接:[https://dolphin scheduler . Apache . org/zh-cn/docs/3 . 0 . 0/user _ doc/guide/how to/general-setting . html](https://dolphinscheduler.apache.org/zh-cn/docs/3.0.0/user_doc/guide/howto/general-setting.html?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE)

## 07 任务定义列表

对于以前版本的 Apache dolphin scheduler 3 . 0 . 0-alpha，如果用户想要操作一个任务，他们需要找到相应的工作流，并在工作流中定位任务，然后才能编辑它。然而，当工作流的数量变大或者单个工作流有大量任务时，查找相应任务的过程对用户来说变得非常痛苦，这不符合 Apache DolphinScheduler 的易用理念。因此，我们在 3.0.0-alpha 中添加了一个任务定义页面，允许用户通过任务名称快速定位和编辑任务，从而允许轻松地进行批量任务更改。

更多详情见链接:[https://dolphin scheduler . Apache . org/zh-cn/docs/latest/user _ doc/guide/project/task-definition . html](https://dolphinscheduler.apache.org/zh-cn/docs/latest/user_doc/guide/project/task-definition.html?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE)

## 08 新警报类型

3.0.0-alpha 警报类型增加了对 Telegram 和 Webexteams 警报类型的支持。

## 09 新的 Python API 特性

Python API 在 3.0.0-alpha 中最大的变化是将 PythonGatewayServer 对等项集成到 API-Server 服务中，这使得外部服务支持更加有组织，并缓解了服务分类导致的大型二进制包的问题。Python API 还添加了 CLI 和配置模块，允许用户更容易地定制配置文件和修改配置。

## 10 个其他新功能

除了上面提到的功能，3.0.0-alpha 版本还增强了许多详细的增强功能，如重构任务插件和数据源插件模块以使缩放更容易，恢复对 Spark SQL 的支持，E2E 测试现在与新的 UI 完美兼容，等等。

# 关键优化

【 [#8584](https://github.com/apache/dolphinscheduler/pull/8584?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) 】任务后端插件优化，新增插件只需修改插件自带模块

[ [#8874](https://github.com/apache/dolphinscheduler/issues/8874?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]在工作流下提交/创建 cron 时验证结束时间和开始时间

【 [#9016](https://github.com/apache/dolphinscheduler/issues/9016?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) 】依赖增加依赖时可以选择全局项目

[ [#9221](https://github.com/apache/dolphinscheduler/issues/9221?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ] AlertSender 优化并优雅关闭，如 MasterServer

[ [#9228](https://github.com/apache/dolphinscheduler/pull/9228?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]实现使用插槽扫描数据库

[ [#9230](https://github.com/apache/dolphinscheduler/issues/9230?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]通过将 python gatewar 迁移到 apiserver 来精简分发包

[ [#9372](https://github.com/apache/dolphinscheduler/pull/9372?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ] [python]将 pythonGatewayServer 迁移到 api 服务器

[ [#9443](https://github.com/apache/dolphinscheduler/pull/9443?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ] [python]添加缺少的配置文件并连接远程服务器

[ [#8719](https://github.com/apache/dolphinscheduler/pull/8719?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ] [Master/Worker]将任务确认更改为运行回调

[ [#9293](https://github.com/apache/dolphinscheduler/pull/9293?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ] [Master]添加任务事件线程池

# 主要错误修复

[ [#7236](https://github.com/apache/dolphinscheduler/issues/7236?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]无法使用 S3a Minio 创建租户

[ [#7416](https://github.com/apache/dolphinscheduler/issues/7416?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]文本文件忙

【 [#7896](https://github.com/apache/dolphinscheduler/issues/7896?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) 】项目授权后，会生成一个重复的授权项目

[ [#8089](https://github.com/apache/dolphinscheduler/issues/8089?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]启动服务器失败，因为无法连接到 postgresql

[ [#8183](https://github.com/apache/dolphinscheduler/issues/8183?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]消息:未找到数据源插件“spark”。

[ [#8202](https://github.com/apache/dolphinscheduler/issues/8202?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ] MapReduce 生成的命令内置参数位置错误

[ [#8751](https://github.com/apache/dolphinscheduler/issues/8751?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]更改参数用户，队列在过程定义中不起作用

[ [#8756](https://github.com/apache/dolphinscheduler/issues/8756?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]使用依赖组件的流程不能在测试和生产环境之间迁移

[ [#8760](https://github.com/apache/dolphinscheduler/issues/8760?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]资源文件删除条件

[ [#8791](https://github.com/apache/dolphinscheduler/pull/8791?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]修正在编辑复制节点的表单时影响原始节点数据的问题。

人力资源耗尽，导致停工

[ [#9243](https://github.com/apache/dolphinscheduler/issues/9243?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE) ]某些类型的报警不能显示项目名称

# 版本注释

[https://github . com/Apache/dolphin scheduler/releases/tag/3 . 0 . 0-alpha](https://github.com/apache/dolphinscheduler/releases/tag/3.0.0-alpha?accessToken=eyJhbGciOiJIUzI1NiIsImtpZCI6ImRlZmF1bHQiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOiJhY2Nlc3NfcmVzb3VyY2UiLCJleHAiOjE2NTA3ODYyNzgsImZpbGVHVUlEIjoibTRrTUw1d3hnTkNwWkRxRCIsImlhdCI6MTY1MDc4NTk3OCwidXNlcklkIjo1MDA5ODc1Mn0.DhzDR_B1I18wfQy3-7uKEfHJVQHXAUw3WEBwXpNLPxE)

# 感谢贡献者

按字母顺序

、Amy0104、Assert、宝良、本尼迪克特金、BenjaminWenqiYu、Brennan Fox、Devosend、丁鹏飞、杜超佳友、EdwardYang、Eric Gao、Frank Chen、高天铎、HanayoZz、、Ivan0626、Jeff Zhan、Jiajie Zhong、JieguangZhou、Jiezhi。g，李今庸，J-Y，科温，凯文。Shin、KingsleyY、Kirs、KyoYang、、刘博东、Manhua、Martin Huang、Maxwell、、OS、QuakeWang、ReonYu、SbloodyS、、Cheng、ShuiMuNianHuaLP、ShuoTiann、Sunny Lei、Tom、Tq、阮文俊、X&Z、XiaochenNan、、Zonglei Dong、acodingaddictic、aaronlinv、caiyun、calvinit、cheney、chouc、gaojun2048、guoshupei、hjli、黄小海 风、烦恼、祥子豪、、小乖乖、徐辉、杨云熙、yc322、、一麦新晨、zchong、李、张、张心如、站前、庄冲、zhuxt2015、zixi0825、zwZjut、田畴、、、、、百岁、洪树、、罗明涛

# 加入社区

参与 DolphinScheduler 社区并为其做出贡献的方式有很多，包括:

**文件、翻译、问答、测试、代码、文章、主题演讲等。**

我们假设第一个 PR(文档、代码)是简单的，应该用来熟悉提交过程和社区协作风格。

所以社区整理了以下适合新手的问题列表:【https://github.com/apache/dolphinscheduler/issues/5689

非新手问题列表:[https://github.com/apache/dolphinscheduler/issues?q = is % 3A open+is % 3A issue+label % 3A % 22 volunteer+wanted % 22](https://github.com/apache/dolphinscheduler/issues?q=is%3Aopen+is%3Aissue+label%3A%22volunteer+wanted%22)

如何参与投稿:[https://dolphin scheduler . Apache . org/en-us/community/development/contribute . html](https://dolphinscheduler.apache.org/en-us/community/development/contribute.html)

**GitHub 代码库:**【https://github.com/apache/dolphinscheduler】T4

**官方网站:**https://dolphinscheduler.apache.org/

邮件列表:dev@dolphinscheduler@apache.org

**推特:**@海豚时间表

YouTube:https://www.youtube.com/channel/UCmrPmeE7dVqo8DYhSLHa0vA

**松弛:**https://s.apache.org/dolphinscheduler-slack

**投稿指南:**https://dolphin scheduler . Apache . org/en-us/community/index . html

你的项目之星很重要，不要犹豫，点亮阿帕奇海豚调度❤️之星