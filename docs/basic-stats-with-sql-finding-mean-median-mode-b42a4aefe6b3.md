# SQL 的基本统计:寻找平均值，中位数，模式。

> 原文：<https://medium.com/codex/basic-stats-with-sql-finding-mean-median-mode-b42a4aefe6b3?source=collection_archive---------7----------------------->

在这篇文章中，我将使用 PostgreSQL，尽管有些函数来自标准 SQL。

## 平均值

> 所有值的总和除以值的个数

为了计算平均值，我们需要使用聚合函数，这是对整个列执行计算的函数。

```
SELECT avg(column) AS column_avg
FROM table;
```

## 中位数

> 有序值集中的中间值