# 不再偏斜:优化 Apache Spark 的创造性方法

> 原文：<https://medium.com/codex/skew-no-more-creative-ways-to-optimize-apache-spark-b39860cc7ac2?source=collection_archive---------5----------------------->

![](img/39bfca3c4837d081a323f5f848bfe6d7.png)

照片由杰克逊在 Unsplash 煨

不对称是分布式系统中的一个常见问题，它会对 Apache Spark 应用程序的性能和效率产生重大影响。当少数任务或执行者接收到不成比例的数据量或工作量，导致他们比其他任务花费更长的时间来完成时，就会发生偏斜。这可能导致瓶颈、不均衡的资源利用率和次优的性能。

有几种方法可以优化 Apache Spark 中的 skew，最佳方法取决于应用程序和数据的具体细节。处理不对称的一些常见策略包括:

对数据进行分区:处理不对称最有效的方法之一是对数据进行分区，在任务和执行者之间平均分配工作负载。在 Apache Spark 中，可以使用哈希分区、范围分区和自定义分区等方法对数据进行分区。例如，以下代码使用 partitionBy 方法根据特定列中的值对 DataFrame 进行分区:

```
val df = spark.read.csv(“/path/to/data.csv”)

val dfWithPartitions = df.repartition(8, $”columnName”)
```

使用联合和重新分区:联合和重新分区方法可用于减少或增加数据帧中的分区数量。这些方法对于处理不对称非常有用，因为它们有助于在任务和执行器之间均匀地分布数据。例如，以下代码使用 repartition 方法将数据帧中的分区数量增加到 16:

```
val df = spark.read.csv(“/path/to/data.csv”)

val dfWithMorePartitions = df.repartition(16)
```

使用专门的算法:有些算法和操作比其他算法和操作对倾斜更敏感。例如，reduce-by-key 操作中的 reduce 操作可能对 skew 特别敏感，因为它组合了所有分区的结果。在这些情况下，使用专门设计的算法来更有效地处理偏斜会很有帮助。例如，sortByKey 方法可用于按键对数据进行排序，这有助于减少不对称对 reduce 操作的影响。以下代码显示了使用 sortByKey 方法按特定列中的值对 DataFrame 进行排序的示例:

```
val df = spark.read.csv(“/path/to/data.csv”)

val dfSortedByKey = df.sortByKey($”columnName”)
```

使用缓存和广播:缓存和广播对于处理不对称非常有用，因为它们有助于减少在任务和执行器之间被混洗和传输的数据量。缓存可用于将数据存储在内存中，因此任务可以快速访问这些数据。广播可用于向每个执行者发送数据的副本，因此不必通过网络进行混洗和传输。例如，以下代码显示了如何使用缓存和广播方法来优化 Spark 应用程序:

```
val df = spark.read.csv(“/path/to/data.csv”)

val dfCached = df.cache()

val broadcastVar = spark.sparkContext.broadcast(dfCached)
```

总之，数据倾斜是 Apache Spark 中的一个常见问题，它会影响分布式应用程序的性能。为了优化 Apache Spark 中的不对称，可以使用诸如重新分区、定制分区、数据采样和数据压缩等技术。您还可以使用 shuffle 服务、缓存和持久性等工具和策略来提高数据洗牌和分区的效率和性能。通过应用这些技术和工具，您可以有效地解决 Apache Spark 中的数据不对称问题，并提高应用程序的性能。

> 请关注我，了解关于数据工程和数据科学的最新消息: [Paul Scalli](https://medium.com/u/1578151e227b?source=post_page-----b39860cc7ac2--------------------------------)