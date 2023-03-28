# 在 Google BigQuery 中使用窗口函数

> 原文：<https://medium.com/codex/using-window-functions-in-google-bigquery-1f55dcfc3f22?source=collection_archive---------3----------------------->

## 如何以及何时使用窗口函数 BigQuery SQL

![](img/153f0561a0380f6c5d1770b051b0c475.png)

塞尔吉·马尔罗在 [Unsplash](https://unsplash.com/s/photos/window?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

什么是窗口函数，如何在 BigQuery SQL 中使用它，更重要的是有哪些用例？

SQL 中的窗口函数是一种分析函数，它使用来自一个或多个元组的值来为每个元组返回一个值。这与聚合函数相反…