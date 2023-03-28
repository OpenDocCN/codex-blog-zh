# SQL Server 查询存储简介

> 原文：<https://medium.com/codex/introduction-to-sql-server-query-store-9e45a563a502?source=collection_archive---------6----------------------->

## 查询存储为您做了什么？

## 介绍

你有没有经历过你的系统变慢或者完全瘫痪？会发生什么？大家都在等你尽快修复问题！老板在你桌子上面！

您是否曾经历过将应用程序升级到最新版本的 SQL Server，并面临计划变更导致应用程序变慢的问题？查询计划选择更改会导致这些问题。

与系统数据库 [tempdb](https://docs.microsoft.com/en-us/sql/relational-databases/databases/tempdb-database?WT.mc_id=DP-MVP-4029181) 相关的性能问题不像与查询计划更改相关的问题那样难以修复。您知道，查询计划的更改会给您带来越来越多的问题。就 [tempdb](https://docs.microsoft.com/en-us/sql/relational-databases/databases/tempdb-database?WT.mc_id=DP-MVP-4029181) 而言，你可以把它移到一个更快的硬盘上，或者你可以增加 [tempdb](https://docs.microsoft.com/en-us/sql/relational-databases/databases/tempdb-database?WT.mc_id=DP-MVP-4029181) 文件的数量，但是当你必须找到运行缓慢的查询时，你必须找出它们为什么慢！

检测和修复运行缓慢的查询所面临的问题需要很长时间，因为您必须研究计划的变化，并且一个问题在您的脑海中出现:以前的计划是什么样的？所以您试图找到这个问题的答案，但是，服务器上的 SQL Server [数据收集器](https://docs.microsoft.com/en-us/sql/relational-databases/data-collection/data-collection?WT.mc_id=DP-MVP-4029181)是否已经激活？如果数据收集器尚未激活，那么调查运行缓慢的查询的唯一工具就是计划缓存，但它可能不适合进行故障排除。当服务器上出现内存压力时，您正在查找的查询可能已经离开了缓存。最后，当您有问题时，您能修改查询文本吗？如果不能，你知道创建和管理[计划指南](https://docs.microsoft.com/en-us/sql/relational-databases/performance/plan-guides?WT.mc_id=DP-MVP-4029181)的系统存储过程吗？

假设给你一个有两个谓词的查询，其中一个谓词的选择性最高，那么哪个计划是最好的呢？

假设您有一个包含两个连接表的查询，例如，您将表 A 连接到表 b，那么实现连接的最佳方式是什么？a 与 B 相连还是相反？

假设现在有一个有 80 个联合表的查询，查询优化器需要为一个有 80 个联合表的查询评估多少个执行计划？可能有数百万个执行计划需要评估！SQL Server 查询优化器会考虑许多计划，但是当数据发生变化时，它可能会选择不同的计划。

## 查询存储为您做了什么？

[查询存储库](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?WT.mc_id=DP-MVP-4029181)存储每个查询的所有计划选择和相关性能指标，它识别最近变得较慢的查询，并允许 DBA 轻松地强制执行计划！如果您为特定查询强制执行计划，它将确保您的更改在服务器重启、升级、故障转移和查询重新编译后仍然有效。

## 查询存储如何捕获数据？

SQL Server 每次编译查询时，都会有一条编译消息进入查询存储区，并存储查询的执行计划。同样，当执行查询时，执行消息进入查询存储，运行时统计信息在查询执行后存储到查询存储中。查询存储根据您选择的时间粒度聚合信息，聚合将在内存中完成(因为我们不想终止服务器)，然后，基于数据库选项[DATA _ FLUSH _ INTERVAL _ SECONDS](https://docs.microsoft.com/en-us/sql/relational-databases/performance/how-query-store-collects-data?WT.mc_id=DP-MVP-4029181)，聚合的数据将以类似于检查点机制的后台异步方式存储在磁盘上。

![](img/d66c6f09c0d6539c6c196e5ae1dddee5.png)

查询存储如何捕获数据

我刚刚告诉过您，聚合是在内存中完成的，而不是在磁盘上完成的，所以假设发生了意外的关机，会有多少内存中的数据丢失呢？如果您在内存中保存较少数量的数据，您将会有较大的 IO 成本，并且在意外关机的情况下会丢失较少数量的信息；否则，如果您在内存中保存较多数量的数据，在意外关机的情况下会丢失较多数量的数据，但是您的 IO 成本会较小。这个设置是你自己选的！重要的是你有选择的可能性！如果您想要查看由查询存储捕获的数据，您需要一个结合内存和磁盘上统计信息的工具。每个 DMV 都与查询存储相关，连接内存和磁盘上的数据。例如，[sys . query _ store _ runtime _ stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql?WT.mc_id=DP-MVP-4029181)将内存和磁盘上的数据集中在一个统一的地方，因此您可以在脚本或应用程序中使用它。

让我们讨论一下查询存储如何解释查询文本。查询文本是语句第一个标记的第一个字符和最后一个标记的最后一个字符之间的字符部分。前面或后面的注释和空格对查询文本的解释没有任何影响。相反，真正重要的是查询文本中的注释和空格。采用这种逻辑来确定文本之间的差异，每个唯一的查询文本在 DMV[sys . query _ store _ query _ text](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql?WT.mc_id=DP-MVP-4029181)中只存储一次。这个 DMV 中最重要的列是*查询 _ 文本 _id* 和*语句 _ sql _ 句柄*。特别是， *query_text_id* 用于标识您强制执行查询计划的查询。

## 查询存储 DMV 概述

下图概述了与查询存储相关的最重要的 dmv。为了便于阅读，我简化了车管所的名称，无论如何，你可以在下面的描述中找到每个车管所的真实名称。

![](img/a1b5a1d5f0b57a0923e55a039c11dcb3.png)

查询存储 DMVs 概述

每次我们运行查询时，SQL 引擎都知道查询的文本，因此查询存储可以将其存储到 Query_Text(真实名称是[sys . Query _ Store _ Query _ Text](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql?WT.mc_id=DP-MVP-4029181))中，这是该模式的起点。如您所知，执行计划受 ANSI 选项的影响，因此对于 Query_Text 的每一行，我们可以在 Query_ANSI 中有几个不同的行(真实名称是 [sys.query_store_query](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?WT.mc_id=DP-MVP-4029181) )，每一行对应 ANSI 选项的一个唯一组合。对于 ANSI 选项的每个独特组合，我们可以有几个不同的执行计划。所有的执行计划始终存储在 Query_Plan(真名为 [sys.query_store_plan](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-plan-transact-sql?WT.mc_id=DP-MVP-4029181) )中；SQL Server 早期版本上的等效 DMV 是 [sys.dm_exec_query_plan](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql?WT.mc_id=DP-MVP-4029181) ，但它只存储最后一个执行计划，您无法知道特定查询的前一个计划是什么。Runtime_Stats(真实名称是[sys . query _ store _ runtime _ Stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql?WT.mc_id=DP-MVP-4029181))包含执行指标，它们以这样的方式存储:每个时间粒度每个计划一行。假设时间粒度设置为 1 分钟，一个查询每分钟执行三次。对于这个场景，在 Runtime_Stats 中，我们有一行对这一分钟内的执行指标进行了分组。

先说最下面最后两个家伙，右边那个是 Runtime_Stats_Interval(真名是[sys . query _ store _ Runtime_Stats _ Interval](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql?WT.mc_id=DP-MVP-4029181))，是 Runtime _ Stats 的细节。它在没有执行计划信息的情况下，每个时间粒度存储一行。另一个是 Context_Settings(真实名称是[sys . query _ Context _ Settings](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-context-settings-transact-sql?WT.mc_id=DP-MVP-4029181))，它为 set 选项的每个唯一组合存储一行。假设一个查询由两个不同的提供者执行，您将有两个上下文设置，每个提供者一个。

## 查询存储优化

因为像这样跟踪指标可能会很昂贵，所以 SQL Server 允许您优化查询存储。您可以调整许多因素，包括以分钟为单位的聚合窗口大小(INTERVAL_LENGTH_MINUTES)、以 MB 为单位的分配给查询存储的最大空间(MAX_STORAGE_SIZE_MB)、为每个查询存储的最大执行计划数(MAX_PLANS_PER_QUERY)等等。您还可以告诉查询存储只记录满足特定条件的查询。SQL Server 允许您通过 [ALTER DATABASE](https://docs.microsoft.com/en-us/sql/t-sql/statements/alter-database-transact-sql?WT.mc_id=DP-MVP-4029181) 语句来调整这些选项。

要了解更多细节，您可以访问页面[使用查询存储](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?WT.mc_id=DP-MVP-4029181)监控性能。

在下一篇文章中，我将讨论与查询计划选择更改相关的性能问题，以及查询存储如何帮助我们识别变得较慢的查询！

在此保持联系！