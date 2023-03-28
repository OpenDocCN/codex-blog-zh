# BigQuery 中的“规格化和 CASEFOLD”

> 原文：<https://medium.com/codex/normalize-and-casefold-in-bigquery-675c670976b0?source=collection_archive---------8----------------------->

## 使用字符串时如何提高数据质量

![](img/0ae6a10512b01f8747a6d39fdfc2f0d7.png)

照片由[阿曼达·琼斯](https://unsplash.com/@amandagraphc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/words?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

使用 BigQuery SQL 函数`NORMALIZE_AND_CASEFOLD`,您很有可能使用不同 Unicode 编码的字符串。

## 什么是 Unicode 和不同的编码？