# 在 BigQuery 中使用默认值

> 原文：<https://medium.com/codex/using-default-values-in-bigquery-a4927713cb8a?source=collection_archive---------8----------------------->

## 如何在 BigQuery SQL 中指定默认值

![](img/ef48f9c8f06d840de77ff08587ffcf9e.png)

[梅里达勒](https://unsplash.com/@meric?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/highway?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Google 现在允许您为 BigQuery 表中的列设置默认值。在此阅读如何使用这一新功能。

有了这个新功能，Google Big Query 提供了一些优势。通常，有所谓的标志列，它们在数据库中已经有了一个默认值。一个典型的例子是 Y/N 或…