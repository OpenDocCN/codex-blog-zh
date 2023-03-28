# 令人敬畏的新功能:在谷歌大查询中改变历史

> 原文：<https://medium.com/codex/awesome-new-feature-change-history-in-google-bigquery-e84e824ce85a?source=collection_archive---------1----------------------->

## 在 BigQuery 中使用追加更改历史 TVF

![](img/9bd4efe200dd41909232181ac714e358.png)

图片由 [C M](https://unsplash.com/@ubahnverleih?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/archive?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

当您通过 ETL 或 ELT 过程将数据从源系统加载到 Google 的 SaaS 数据仓库 BigQuery 时，您希望在一个表中见证并记录`INSERT`、`UPDATE`或`DELETE`。我已经在本文的[中描述了如何实现这样的版本控制。](/codex/collaborate-better-with-data-versioning-566c2299c435)