# 在 BigQuery 中使用 Max Staleness 实体化视图选项

> 原文：<https://medium.com/codex/using-the-max-staleness-materialized-view-option-in-bigquery-d5977a8ed8fc?source=collection_archive---------8----------------------->

## 如何在谷歌的数据仓库中更好地控制你的成本

![](img/b3143af5e2d8881c6b6d7b577a6b519e.png)

克劳迪奥·泰斯塔在 [Unsplash](https://unsplash.com/s/photos/nature?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Google 刚刚宣布了一个在使用物化视图时为 BigQuery 提供的又好又方便的新特性:物化视图选项[1]。使用此选项，您可以创建具有一致高性能的视图，同时在处理时控制成本…