# 粘合分区索引:加速查询

> 原文：<https://medium.com/codex/glue-partition-indexes-speed-up-your-queries-bde5dcaab0c0?source=collection_archive---------5----------------------->

![](img/5f34969cf4d31e0692c8cf7f6c1f8d6f.png)

这一周还是不能戒掉吸胶水的习惯

# 介绍

我在之前的[帖子](/@matt_weingarten/migrating-to-a-glue-metastore-e6e7a2cd424f)中谈到了从 Hive metastore 迁移到 Glue metastore，以及我们在迁移过程中面临的一些挑战。即使在这次迁移之后，我们注意到我们的查询在 Databricks 中运行的时间比在 Qubole 中长。为什么会这样呢？这是具有许多分区的数据集的结果。既然这是不可避免的，我们该如何解决呢？

# 分区索引

粘合分区索引是可以添加到粘合表中以加快查询速度的附加组件。本质上，分区索引应该以您在查询中使用的最常见的分区列为目标，以便在将来将它们添加到过滤器中时，您可以从中获益。对于那些使用 IaC 的人来说，有一个分区索引的 Terraform 模块，以便这些可以应用于表(甚至在创建时间之后，尽管我在尝试这种方法时面临加密分区错误的问题；通过在添加分区之前重新创建表并添加索引，我能够正确地创建分区索引)。

# 警告

值得注意的是，如果在一个索引中指定多个列，那么列表中第一列的存在对于利用分区索引来说是至关重要的。例如，如果您的分区索引是[country，year，month，day]，请确保 country 存在(并确保您的索引是结构化的，因此第一列是最关键的一列)。

同样值得注意的是，如果您想真正正确地使用索引，只允许对查询进行某些操作。例如，与其他操作相比，使用 IN 运算符实际上不会激活索引。所有这些警告都可以在 AWS 的[文档](https://docs.aws.amazon.com/glue/latest/dg/partition-indexes.html)中找到。

# 影响

随着我们继续进行测试，我肯定可以用实际的比较来更新这篇文章。我在文档中找不到的一件事是，在表上建立索引是否会给基于该表的视图带来这些好处。这就是我们通常组织数据的方式(因为视图比表有更多可读的列名)，所以如果它不能继续的话，就没有优势了。

无论哪种方式，我们都希望利用分区索引。迁移到像 Databricks 这样的工具，并看到我们新的 metastore 实现导致即席查询的性能下降，这确实令人沮丧，因此希望这有助于稳定该领域，因为我们将继续专注于在其他地方加强我们的最佳实践。

# 结论

类似的优化(在 Athena 方面)是[分区投影](https://docs.aws.amazon.com/athena/latest/ug/partition-projection.html)。我自己从来没有尝试过，因为我们通常不使用 Athena 进行任何分析，但对于那些使用它的人来说，这似乎是一个很大的改进。从分区的角度来看，拥有自动化分区管理和能够使用实际有效的日期范围是非常必要的，对于那些在日常工作中使用 Athena 的人来说，这是值得一试的。

否则，考虑使用分区索引来满足您的粘合需求。希望这对你有用！