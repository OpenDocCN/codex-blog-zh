# 在 BigQuery 中将整数转换为日期

> 原文：<https://medium.com/codex/convert-integer-to-date-in-bigquery-a114f8f2281a?source=collection_archive---------3----------------------->

## 如何解析数字并将其转换为日期时间格式

![](img/da7d7eeb7520c5f18b44ab34716f1041.png)

在 [Unsplash](https://unsplash.com/s/photos/number?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[Towfiqu barb huya](https://unsplash.com/@towfiqu999999?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

大多数时候，你可能会将一个字符串转换成日期([这里](/codex/convert-string-to-date-in-bigquery-94e61f31fce4)是这样做的指南)、时间或日期时间数据类型，但是从我的经验来看，我知道特别是老的数据库喜欢将日期存储为一个数字，例如一个整数。对于分析任务或数据集成过程，将…