# 一直以来，我都在使用 SQL 反模式

> 原文：<https://medium.com/codex/all-along-ive-been-using-a-sql-antipattern-50f9a6232f89?source=collection_archive---------7----------------------->

## 下面是如何修复它…

![](img/00f431e94d447a9d8dfabf9daf50bedc.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的[回声网格](https://unsplash.com/@echogrid?utm_source=medium&utm_medium=referral)拍摄

几个月前，我写了几个 MYSQL 表，以便在学习用 Python API 创建 [JSON web 令牌时使用。一个相当简单的任务，然而，最近我注意到我犯了一个错误。对于主键，我使用了一个表示 GUID(全局唯一标识符)的 CHAR 值。因为 MYSQL 没有 GUID…](https://python.plainenglish.io/json-web-tokens-with-python-apis-part-1-creating-the-token-ce2cfe22b7d6?source=your_stories_page-------------------------------------)