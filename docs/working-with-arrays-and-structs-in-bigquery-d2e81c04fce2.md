# 在 BigQuery 中使用数组和结构

> 原文：<https://medium.com/codex/working-with-arrays-and-structs-in-bigquery-d2e81c04fce2?source=collection_archive---------8----------------------->

## 如何高效地存储您的数据

![](img/0b26f46aaba221aa2e2fcb473caf5db8.png)

乔纳森·库珀在 [Unsplash](https://unsplash.com/s/photos/pine?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

较新的数据仓库(如 BigQuery)以及其他解决方案通常处理非规范化数据，其背景是**这些技术更好地处理基于列的表。**结构和数组用于避免在反规范化期间复制数据集。在这篇文章中，你将很快了解这到底意味着什么…