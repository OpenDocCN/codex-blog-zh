# 在 BigQuery 中使用会话模式

> 原文：<https://medium.com/codex/using-session-mode-in-bigquery-f17709fe696b?source=collection_archive---------5----------------------->

## 如何使用谷歌大查询的新功能

![](img/a362beb75140cdfbb84d234904dc891c.png)

由[文森特·范·扎林格](https://unsplash.com/@vincentvanzalinge?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/nature?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Google 发布了 BigQuery 的会话模式，你可以使用它来自动或手动终止会话，为会话中的所有查询设置标签，并在`INFORMATION_SCHEMA.SESSIONS_BY_*`视图中获取会话元数据[1]。使用会话模式，您可以在 BigQuery 中使用临时表和变量，在整个会话期间…