# 在 BigQuery SQL 中使用 WITH RECURSIVE

> 原文：<https://medium.com/codex/using-with-recursive-in-bigquery-sql-e593325f33f?source=collection_archive---------2----------------------->

## 该特性现已添加到 Google 标准 SQL for BigQuery 中

![](img/e88f5a34c00f903923793dddc0d01c37.png)

Photo by [愚木混株 cdd20](https://unsplash.com/@cdd20?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/loop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Google 标准 SQL for BigQuery [1]中添加了`WITH RECURSIVE`特性。然而，只是在预览版，但像以前一样，每个人都可能在未来获得该功能。这个特性允许一个查询在一个`WITH`子句中引用它自己或者引用后面定义的查询…