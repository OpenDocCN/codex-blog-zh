# SQL Server 2022 并行度(DOP)反馈

> 原文：<https://medium.com/codex/sql-server-2022-degree-of-parallelism-dop-feedback-10af7136dc80?source=collection_archive---------9----------------------->

## MAXDOP 配置的最终解决方案！

![](img/74347f07e0d18785aec4b19036cfab16.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

并行度(DOP)反馈是 SQL Server 2022 的新特性之一，也是[智能查询处理(IQP)](https://docs.microsoft.com/en-us/sql/relational-databases/performance/intelligent-query-processing?WT.mc_id=DP-MVP-4029181) 系列特性的一部分。这些智能和自适应特性可以提高现有工作负载的性能，而无需更改应用程序代码。在本文中，我将讨论 MAXDOP 选项，以及 DOP 反馈如何让您优化资源的使用并提高工作负载的可伸缩性，因为过度的并行性会导致性能问题，尤其是对于以 OLTP 为中心的查询。

## 并行度反馈

SQL Server 2022 中的 DOP 反馈通过解决在并行模式下重复执行 OLTP 查询并遇到性能问题时出现的情况，进一步增强了智能查询处理。查询执行计划中的并行性对于分析查询或处理大量数据的查询通常很有用。无论如何，当协调所有线程所花费的时间超过使用并行计划的优势时，以并行模式执行的以 OLTP 为中心的查询可能会遇到性能问题。

在 SQL Server 2019 之前，最大并行度(MAXDOP)服务器配置选项和 MAXDOP 数据库范围配置的默认值设置为零(0)。值零(0)告诉 SQL Server 引擎在使用并行执行计划处理查询时使用所有可用的计划程序。SQL Server 2019 更改了 MAXDOP 默认设置。本地实例中的默认服务器配置选项是根据安装时检测到的处理器的计算结果设置的。对于 Azure SQL 数据库，MAXDOP 数据库范围的配置已更改为默认值 8。有关配置 MAXDOP 选项的更多详细信息，请参阅文章[配置最大并行度服务器配置选项](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option?WT.mc_id=DP-MVP-4029181)。

新 DOP 反馈功能的目标是自我调整 MAXDOP 选项，以通过消除由于确定 MAXDOP 的无所不包的默认设置和对每个查询的手动修改而引起的困难来避免上述问题。

## DOP 反馈是如何工作的？

DOP 反馈将根据查询运行时间和遇到的[等待类型](https://docs.microsoft.com/it-it/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql?WT.mc_id=DP-MVP-4029181)(通过忽略并行查询执行之外的缓冲区闩锁、缓冲区 IO 和网络 IO 等待来调整每个查询的总运行时间)，为您识别由于重复查询的并行性而导致的低效率。如果并行性的使用被认为是低效的，DOP 反馈将在查询的下一次执行中降低并行度。无论配置的并行度如何，都将触发该机制。使用新配置检测到的运行时间和等待类型将与以前的进行比较，以确定是否有改进。

为了评估要用 DOP 反馈处理的查询的适用性，在查询本身的一些执行上测量查询运行时间。目标是提高整体并发性并减少等待，即使这稍微增加了查询运行时间。只有经过验证的反馈才会被保留。

如果对并行度的校正将产生性能回归，则并行度将被还原为查询的最后一个已知有效值。在这种情况下，即使被用户取消的查询也会被认为是一种回归。DOP 反馈不会重新编译执行计划。

在每次重新编译执行计划时，会检查并行度的稳定修正，并且可能会重新向上调整或再次向下修订，但绝不会超过 MAXDOP 设置(包括 MAXDOP 提示)。有关更多信息，请观看视频 Azure SQL & SQL Server 2022:智能数据库未来:

要在数据库级别启用 DOP 反馈，使用 [ALTER DATABASE](https://docs.microsoft.com/it-it/sql/t-sql/statements/alter-database-transact-sql?WT.mc_id=DP-MVP-4029181) 命令并将 DOP_FEEDBACK 选项设置为 on，如下面的 T-SQL 代码所示。

```
-- Enable DOP feedback at the database level
ALTER DATABASE SCOPED CONFIGURATION SET DOP_FEEDBACK = ON;
GO
```

若要在数据库级别禁用 DOP 反馈，请将 DOP_FEEDBACK 选项设置为 OFF，如下面的 T-SQL 代码所示。

```
-- Disable DOP feedback at the database level
ALTER DATABASE SCOPED CONFIGURATION SET DOP_FEEDBACK = OFF;
GO
```

若要在查询级别禁用 DOP 反馈，请使用 DISABLE_DOP_FEEDBACK 查询提示。

如果通过查询存储强制执行查询计划，DOP 反馈仍然可以用于优化查询的并行度。

如果您已经在查询代码中或通过查询存储建议直接使用了 MAXDOP 查询提示，并且 MAXDOP 提示大于 2，则 DOP 反馈可以使用以上述方式建议的新限制来进一步降低并行度。有关其他详细信息，您可以参考文章[提示(Transact-SQL) —查询](https://docs.microsoft.com/en-us/sql/t-sql/queries/hints-transact-sql-query?WT.mc_id=DP-MVP-4029181)和[查询存储提示(预览)](https://docs.microsoft.com/en-us/sql/relational-databases/performance/query-store-hints?WT.mc_id=DP-MVP-4029181)。

可以通过 4 个新的扩展事件和 sys.query_store_plan_feedback 系统视图来监控 DOP 反馈的功能。以下是 4 个新的扩展事件:

*   dop_feedback_eligible_query:当查询计划适合 dop 反馈时发生
*   dop_feedback_provided:当查询计划适合 dop 反馈时发生
*   dop_feedback_validation:当根据基线或以前的反馈统计信息对查询运行时统计信息进行验证时发生
*   dop _ feedback _ reverted:当 dop 反馈被恢复时发生

## 摘要

并行度(DOP)反馈是 SQL Server 2022 的新功能之一，是智能查询处理(IQP)系列功能的一部分，它能够自我调整 MAXDOP 选项，以避免由于在重复执行的 OLTP 查询中使用并行性而导致的性能问题。

享受 SQL Server 2022 DOP 反馈！