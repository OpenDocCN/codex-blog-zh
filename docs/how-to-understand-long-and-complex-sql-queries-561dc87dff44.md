# 如何理解长而复杂的 SQL 查询

> 原文：<https://medium.com/codex/how-to-understand-long-and-complex-sql-queries-561dc87dff44?source=collection_archive---------1----------------------->

## 数据科学

## 用于复杂 SQL 查询的“剥离并理解层”策略

![](img/b6dad8e2eabc3dcf7e8f850152c3fcc8.png)

Clark Van Der Beken 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

用于即席分析或报告的单个 SQL 查询可能有 2-40 行(通常)。但是对于数据管道和预定查询，一个 SQL 查询可能有数百行(很容易！相信我)。