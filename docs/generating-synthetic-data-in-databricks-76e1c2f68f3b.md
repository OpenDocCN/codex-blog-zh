# 在数据块中生成合成数据

> 原文：<https://medium.com/codex/generating-synthetic-data-in-databricks-76e1c2f68f3b?source=collection_archive---------8----------------------->

从 QA 的角度来看，交付数据项目并不容易。大多数情况下，您无法访问实际数据，或者只有一部分经过清理的数据用于测试。对数据子集进行 QA 固然很好，但最好是能够访问类似生产的数据。Databricks labs 通过他们的实用程序 [dbldatagen](https://github.com/databrickslabs/dbldatagen) 来拯救我们。它可用于在舒适的 Databricks 笔记本中轻松生成大量合成数据。

## 先决条件

*   如果没有新的 Databricks 工作区，请获取一个。你…