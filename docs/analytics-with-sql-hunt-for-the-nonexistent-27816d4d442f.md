# SQL 分析:寻找不存在的东西

> 原文：<https://medium.com/codex/analytics-with-sql-hunt-for-the-nonexistent-27816d4d442f?source=collection_archive---------18----------------------->

## ~使用 SQL 查找订阅者获取源

![](img/ba22c148f5f271b7e102c60e6b35e7b2.png)

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 背景故事:

正如你从我以前的帖子中所知道的，我已经建立了一个邮件列表来推广我的[博客](https://www.abwrites.blog/)。在本系列的上一篇文章中，我们通过构建一个历史表来分析我的邮件列表行为，设计了一种通过数据回到过去的方法。这一次的计划是通过添加一些味道来扩展它，使我们能够回答这样的问题:人们如何看到我的邮件列表？是什么驱使他们订阅？如果他们重新订阅，是什么原因造成的？我们今天要做的事情看起来就像在黑暗中投掷飞镖，希望击中靶心，如果你问我，我认为这与生活非常相似。所以，提高你的目标，让我们开始吧！

# 问题:

因此，上次我们能够构建自己的 *subscriber_feed_history* 表，该表让我们能够深入了解用户每天的 *mailing_list_status* 。现在，虽然这有助于确定用户在特定的一天是选择加入还是退出，但它并不能告诉我是什么驱使用户采取特定的行动。首先，我想知道是哪个来源导致用户订阅我的邮件列表。这有助于我选择合适的推广平台。

# 数据:

我们从*subscriber _ feed _ history*表开始，我们在这里构建了[表，如下所示:](https://singhdoesdata.medium.com/analytics-with-sql-going-back-in-time-b2eeb819566a)

```
+------+---------+---------------------+
| date | user_id | mailing_list_status |
+------+---------+---------------------+
| 1/23 |     777 | I                   |
| 1/15 |     123 | I                   |
| 1/8  |     456 | I                   |
| 1/4  |     789 | I                   |
+------+---------+---------------------+
```

我还能够使用类似的方法，从*点击流*数据中构建一个 *source_history* 表，用于采集点击(类似于我们在这里使用的[)。此表是用户点击的历史记录，带有记录操作的源。该表如下所示:](/@singhdoesdata/analytics-with-pandas-whats-working-2a29a8d70d07)

```
+-------------+---------+-----------+
| action_date | user_id |  source   |
+-------------+---------+-----------+
| 1/15        |     123 | LinkedIn  |
| 1/7         |     456 | Medium    |
| 1/3         |     456 | Direct    |
| 1/3         |     789 | Instagram |
| 1/1         |     789 | Twitter   |
| 1/1         |     777 | Twitter   |
+-------------+---------+-----------+
```

目的是使用这个 *source_history* 来增强*subscriber _ feed _ history*的选择加入源信息，即用户是如何订阅的，或者他们是如何遇到我的博客的。此外，用户选择订阅可能有不同的原因，因此我们将尽最大努力捕捉这种效果。

```
+------+---------+--------+---------------+
| date | user_id | status | opt_in_source |
+------+---------+--------+---------------+
| 1/23 |     777 | I      | ??            |
| 1/15 |     123 | I      | ??            |
| 1/8  |     456 | I      | ??            |
| 1/4  |     789 | I      | ??            |
+------+---------+--------+---------------+
```

> 停下来。在阅读之前，我希望你在阅读我的方法之前，花些时间自己思考一下这个问题。

# 我的方法:

处理用户数据的一个好方法是考虑[客户旅程](https://www.crazyegg.com/blog/customer-journey/)。在我们的例子中，有这样一个场景，一个用户在 tweet 上看到了我的博客，并决定查看一下。从这里开始，用户要么觉得我的博客有趣，要么决定订阅我的邮件列表。这里是“ *Twitter* 将用户引向我的博客的地方，它将被视为选择加入的来源。

现在，要用数据实现这一点，最简单的方法是在*订阅者 _ 订阅源 _ 历史*和*来源 _ 历史*之间的*用户 _id* 和*日期*上执行连接。这对于上面数据中的 *user_id=123* 非常有效，现在我们知道选择加入的来源是 LinkedIn。

```
+------+---------+---------------------+----------+
| date | user_id | mailing_list_status |  source  |
+------+---------+---------------------+----------+
| 1/15 |     123 | I                   | LinkedIn |
+------+---------+---------------------+----------+
```

然而，这对我们其他人没有帮助。所以让我们想想这种方法什么时候会失败。我可以考虑几个场景，比如用户在某一天看到博客，但决定在以后的某一天订阅邮件列表。这是另一个例子:一个用户在同一天通过多个来源看到这个博客，然后订阅。在这些情况下，所发生的是在*日期*字段上的连接导致了问题，因为这两个表中的日期可能不完全匹配，这看起来是上面大多数用户的情况。

为了说明这一点，而不是加入期望精确匹配的*日期*字段，我们应该找到记录了与后续访问相对应的选择加入的最近日期。例如，对于在 1 月 8 日加入的 *user_id=456* ，这是两个表之间的连接:

```
+---------+-------------+--------+-------------+---------+
| user_id | opt_in_date | status | action_date | source  |
+---------+-------------+--------+-------------+---------+
|     456 | 1/8         | I      | **1/7**         | **Medium**  |
|     456 | 1/8         | I      | 1/3         | Direct  |
+---------+-------------+--------+-------------+---------+
```

在这里，我们可以清楚地说，1 月 8 日的选择加入应归因于*媒介*，因为在 1 月 7 日有相应的访问。如果你想一想是什么让你打这个电话，是*选择 _ 进入 _ 日期*和*行动 _ 日期* ( *增量*)之间的差异促使你做出决定。因此，实际上你正在寻找一个基于这个 *delta* 的排名，这将有助于你进行匹配。

```
+---------+--------+--------+-------------+---------+-------+------+
| user_id | opt_in | status | action_date | source  | delta | rank |
+---------+--------+--------+-------------+---------+-------+------+
|     456 | 1/8    | I      | **1/7 **        | **Medium**  |     **1** |    **1** |
|     456 | 1/8    | I      | 1/3         | Direct  |     5 |    2 |
+---------+--------+--------+-------------+---------+-------+------+
```

在你使用这个解决方案之前，让我们想一想这个问题会在哪里出现。让我们考虑一下 *user_id=777* :

```
+---------+--------+--------+-------------+--------+-------+------+
| user_id | opt_in | status | action_date | source | delta | rank |
+---------+--------+--------+-------------+--------+-------+------+
|     777 | 1/23   | I      | 1/1         | ??     |    22 |    1 |
+---------+--------+--------+-------------+--------+-------+------+
```

在这种情况下，用户在 1 号点击了一个博客，然后在近一个月后最终订阅了邮件列表。使用我们之前的方法， *delta，在本例中为*，变成 22 天，假设这是唯一的访问，它的等级为 1。所以你在这里说的是，用户订阅是因为一个月前与博客的互动。现在我不确定你的记忆力，但是我不能回忆起昨天在网上看到的东西，更不用说一个月了。这里我们想要限制*增量*以防止不正确的匹配。

让我们看看如何用代码写下来:

```
-- All opt-ins from subscriber history
WITH subscriber_history AS (
SELECT date AS opt_in_date, user_id
FROM `*subscriber_feed_history`* WHERE mailing_list_status = "I"
GROUP BY date, user_id
),-- User actions with source
source_history AS (
SELECT action_date, user_id, source
FROM *source_history*
WHERE source <> "Direct"
),-- Delta calculation: (Opt-In date - Click date)
subscriber_source_history AS (
SELECT opt_in_date, user_id, source,
DATE_DIFF(opt_in_date,action_date, DAY) AS delta
FROM subscriber_history 
LEFT JOIN source_history 
USING(user_id)
WHERE delta BETWEEN 0 AND 1
)-- Getting the best match for the source of opt-in
SELECT * EXCEPT(rnk)
FROM (
SELECT * EXCEPT(delta),
RANK() OVER(PARTITION BY opt_in_date, user_id ORDER BY delta) AS rnk
FROM subscriber_history_source
) WHERE rnk = 1
```

如果你想知道为什么*直接*来源被遗漏，阅读[这里](https://useinsider.com/glossary/direct-traffic/#:~:text=Direct%20traffic%20is%20the%20amount,with%20no%20source%20or%20data.)。

现在，如果您对选择加入的源匹配率感到满意，您可以就此打住。如果你想要更好的匹配率，你可以试试上面的 *delta* 。窗口越宽，匹配率越高，但匹配的可靠性越低。或者你可以继续阅读寻找不同的方法。

现在我想谈谈解决同一问题的另一种方法，它完全不依赖于日期。让我们考虑一个用户，他不确定自己是否真的想收到我的邮件，因此最终多次重新订阅。现在，对于每一次重新订阅，用户都以某种方式与博客互动，从而导致它。从点击流中，我们可以知道用户与博客互动的次数，而不考虑日期。因此，与其在日期上匹配，我们可以纯粹地在事件发生时匹配。

以下是 user_id=999 的示例:

```
*subscriber_feed_history:* 
+------+---------+--------+------------+
| date | user_id | status | opt_in_num |
+------+---------+--------+------------+
| 2/4  |     999 | I      |          5 |
| 1/30 |     999 | I      |          4 |
| 1/20 |     999 | I      |          3 |
| 1/8  |     999 | I      |          2 |
| 1/4  |     999 | I      |          1 |
+------+---------+--------+------------+*source_history:* 
+-------------+---------+-----------+-----------------+
| action_date | user_id |  source   | interaction_num |
+-------------+---------+-----------+-----------------+
| 1/31        |     999 | Instagram |               5 |
| 1/26        |     999 | Twitter   |               4 |
| 1/13        |     999 | Medium    |               3 |
| 1/5         |     999 | Twitter   |               2 |
| 1/1         |     999 | Organic   |               1 |
+-------------+---------+-----------+-----------------+
```

在这种情况下，我们知道用户与博客交互了 5 次，并相应地订阅了邮件列表 5 次。因此，通过这种方法，如果选择加入的数量与互动的数量相匹配，我们将来源与选择加入的数量相匹配。

这在 SQL 中是这样的:

```
-- All opt-ins with opt-in number
WITH subscriber_history AS (
SELECT date AS opt_in_date, user_id,
RANK() OVER(PARTITION BY user_id ORDER BY date) AS optin_num
FROM `*subscriber_feed_history`* WHERE mailing_list_status = "I"
GROUP BY date, user_id
),-- User actions with source and interaction number
source_history AS (
SELECT user_id, source,
RANK() OVER(PARTITION BY user_id ORDER BY action_date) AS intrcn_num
FROM *source_history*
WHERE source <> "Direct"
)-- Matching available user opt-ins on occurrence
SELECT opt_in_date, user_id, source
FROM subscriber_history sub
LEFT JOIN source_history src
ON sub.user_id = src.user_id
AND sub.optin_num = src.intrcn_num
WHERE src.intrcn_num IS NOT NULL
```

*注意:我们使用这里的日期来计算发生次数，但不用于实际匹配。所以现在日期没有以前那么重要了。*

最后，当你可以两者兼得的时候，为什么要选择一个呢？这里唯一的警告是，当有与这两种方法都匹配的源时，您会希望给予第一种方法比后者更高的优先级。

下面是在 SQL 中如何做到这一点:

```
WITH subscriber_history AS (
SELECT date AS opt_in_date, user_id,
RANK() OVER(PARTITION BY user_id ORDER BY date) AS optin_num
FROM `*subscriber_feed_history`* WHERE mailing_list_status = "I"
GROUP BY date, user_id
),source_history AS (
SELECT action_date, user_id, source, 
RANK() OVER(PARTITION BY user_id ORDER BY action_date) AS intrcn_num
FROM *source_history*
WHERE source <> "Direct"
),subscriber_source_delta AS (
SELECT opt_in_date, user_id, source,
DATE_DIFF(opt_in_date,action_date, DAY) AS delta
FROM subscriber_history 
LEFT JOIN source_history 
USING(user_id)
WHERE delta BETWEEN 0 AND 1
),-- Approach #1: With dates
subscriber_source_history_delta AS (
SELECT * EXCEPT(rnk), 1 AS solution_rank
FROM (
SELECT * EXCEPT(delta),
RANK() OVER(PARTITION BY opt_in_date, user_id ORDER BY delta) AS rnk
FROM subscriber_history_source
) WHERE rnk = 1
),-- Approach #2: With occurrence
subscriber_source_history_occurrence AS (
SELECT opt_in_date, user_id, source, 2 AS solution_rank
FROM subscriber_history sub
LEFT JOIN source_history src
ON sub.user_id = src.user_id
AND sub.optin_num = src.intrcn_num
WHERE src.intrcn_num IS NOT NULL
),-- Combining the two with priority for the same opt-in
subscriber_source_history_combined AS (
SELECT * EXCEPT(solution_rank),
RANK() OVER(PARTITION BY user_id, opt_in_date ORDER BY solution_rank) AS final_solution_rank 
FROM (
SELECT * FROM subscriber_source_history_delta
UNION ALL
SELECT * FROM subscriber_source_history_occurrence
)
)-- Final source with priority #1 over #2
SELECT opt_in_date, user_id, source AS opt_in_source
FROM subscriber_source_history_combined
WHERE final_solution_rank = 1
```

# 思考的食粮:

*   你还可以看看其他什么东西来增强历史数据？对于选择退出的来源，我们能做些什么？
*   对于第一种方法，如果同一天有两个来自不同来源的访问，会怎么样？你如何打破平局？
*   你为什么不依赖数据中的日期？
*   第二种方法有什么缺点？
*   在这两种方法中，为什么订阅者和源历史记录之间的连接是左连接？
*   您还可以通过哪些方式来匹配订阅？

让我知道你会如何处理这件事。如果你发现这很有帮助，请分享。如果你对此感兴趣，你可以在 Twitter[*@ abhishek 27297*](https://twitter.com/abhishek27297)*上找到我的谈话资料。*