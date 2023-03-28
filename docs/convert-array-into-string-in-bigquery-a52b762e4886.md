# 在 BigQuery 中将数组转换为字符串

> 原文：<https://medium.com/codex/convert-array-into-string-in-bigquery-a52b762e4886?source=collection_archive---------3----------------------->

## 如何在 Google BigQuery 中使用数组和文本

![](img/39990628ef1a2eb0a910577c0736408e.png)

照片由[曼纽尔将](https://unsplash.com/@manufactured?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)放在 [Unsplash](https://unsplash.com/s/photos/fir?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在 BigQuery 中，可以使用数组作为零个或许多其他数据类型的排序列表。您可以创建具有简单数据类型(如`STRING`或`NUMBER`)和复杂数据类型(如[结构](/me/stats/post/d2e81c04fce2))的数组。当前的例外是`ARRAY`数据类型，因为不支持数组的数组[1]。