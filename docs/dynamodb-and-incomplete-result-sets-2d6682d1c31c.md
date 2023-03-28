# DynamoDB 和不完整的结果集

> 原文：<https://medium.com/codex/dynamodb-and-incomplete-result-sets-2d6682d1c31c?source=collection_archive---------11----------------------->

![](img/dd97fabdb458045c1511870c93e091de.png)

Alex bljan 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我最近参与的一个项目中，DynamoDB 被用作数据存储。该项目是一个内部公用事业，将得到尖峰利用率。它会在短时间内从零流量变成不可忽略的使用量。再加上管理 DynamoDB 表的操作开销几乎为零，它是作为该工具的数据存储的可靠选择。

## 问题是