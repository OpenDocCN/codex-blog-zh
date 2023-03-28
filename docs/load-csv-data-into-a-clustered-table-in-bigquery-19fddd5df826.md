# 将 CSV 数据加载到 BigQuery 的聚集表中

> 原文：<https://medium.com/codex/load-csv-data-into-a-clustered-table-in-bigquery-19fddd5df826?source=collection_archive---------10----------------------->

## 如何使用 Python 向 Google BigQuery 导入数据

![](img/42601328b46c643a388e29964aec60f0.png)

约翰·福勒在 [Unsplash](https://unsplash.com/s/photos/desert?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

有了 Python 和 BigQuery，您就有了一个非常强大的数据科学工具集。使用 Jupyter 笔记本，您可以访问 BigQuery 并导入、解析、分析数据，如果有必要，还可以将数据加载回 BigQuery。另一个用例当然是将每个 ETL/ELT 作业的数据集成到 BigQuery 的过程…