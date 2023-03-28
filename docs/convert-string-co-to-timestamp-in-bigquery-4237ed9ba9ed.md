# 在 BigQuery 中将字符串转换为时间戳

> 原文：<https://medium.com/codex/convert-string-co-to-timestamp-in-bigquery-4237ed9ba9ed?source=collection_archive---------3----------------------->

## 如何使用 BigQuery SQL 轻松地将输入转换成时间戳

![](img/d6aa06ca5dcb47ee0567413c843fce58.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/timestamp?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

如果使用 BigQuery，您可能需要将其他数据类型转换为时间戳或格式化时间戳本身。为此，Google BigQuery 中有三个方便的函数。

在我向您展示将值转换成`TIMESTAMP`的方法之前，重要的是…