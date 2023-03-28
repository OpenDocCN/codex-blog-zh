# 现在可用:BigQuery 中的多语句事务

> 原文：<https://medium.com/codex/now-available-multi-statements-transactions-in-bigquery-35016d79b68a?source=collection_archive---------0----------------------->

## BigQuery SQL 现在支持多语句事务

![](img/9e7c376427dd361e04f4b3d80b4fdee4.png)

杰克·希尔斯在 [Unsplash](https://unsplash.com/s/photos/mountains?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

BigQuery 现在支持单个查询内的多语句事务，甚至在使用会话[1]时跨多个查询。Google 宣布多语句事务允许你执行变异操作，比如在一个或多个表上插入或删除行…