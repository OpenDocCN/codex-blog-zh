# 从众多解决方案中选择一个:PySpark 转换代码的基准测试

> 原文：<https://medium.com/codex/choosing-one-solution-out-of-many-benchmarking-pyspark-transformation-codes-a5191b5d491b?source=collection_archive---------7----------------------->

像大多数编程语言一样，Spark 中有几种解决问题的方法。对于转换数据的代码来说尤其如此(如果您有兴趣了解更多关于 Spark 转换和动作的信息，请查看这篇[文章](/codex/spark-transformation-and-action-a-deep-dive-f351bce88086))。虽然有不同的选择是好的，但选择一个变得更加棘手。对我来说，这样的问题归结为可读性和性能之间的权衡。在本文中，我们将探讨这一困境。

# 问题是