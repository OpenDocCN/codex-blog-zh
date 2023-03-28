# BigQuery 现在支持查询队列

> 原文：<https://medium.com/codex/bigquery-now-supporting-query-queues-378a65fdc9c1?source=collection_archive---------4----------------------->

## 在 Google BigQuery 中使用查询队列实现并发

![](img/140e2cb9d5078c4cd5e81afd88ba4b44.png)

澳大利亚八月在 [Unsplash](https://unsplash.com/s/photos/1-2-3?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Google 刚刚宣布，预览版中的查询队列现在可用于按需客户和固定费率客户。启用查询后，Google BigQuery 会自动确定查询并发性，而不是设置固定的限制。统一费率的客户可以用自定义的…