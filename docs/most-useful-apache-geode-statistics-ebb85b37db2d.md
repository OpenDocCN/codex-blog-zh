# 最有用的 Apache Geode 统计数据

> 原文：<https://medium.com/codex/most-useful-apache-geode-statistics-ebb85b37db2d?source=collection_archive---------5----------------------->

![](img/9b2a63fd3b2035a13d0499c0d19d091e.png)

# 介绍

Apache Geode 在分布式系统的每个成员中生成各种统计数据，包括以下统计数据:

*   操作系统
*   Java 虚拟机(JVM)
*   JVM 堆内存
*   JVM 垃圾收集
*   对等请求
*   客户端到服务器的请求
*   缓存性能

如果*启用统计采样的*属性设置为真，则统计数据会定期写入由*统计存档文件*属性配置的存档文件中。查看文件的主要方式是使用[可视化统计显示](https://gemtalksystems.com/products/vsd/) (vsd)工具。参见文档[此处](https://gemfire.docs.pivotal.io/910/geode/managing/troubleshooting/producing_troubleshooting_artifacts.html)了解生成统计文件的更多细节。有关 vsd 的更多详细信息，请参见文档[此处](https://gemfire.docs.pivotal.io/910/gemfire/tools_modules/vsd/chapter_overview.html)。

其中一些统计数据有助于解决大多数问题；有些比较晦涩，只适用于狭窄的情况。

本文描述了对问题进行故障排除时最有用的统计信息，在某些情况下，还描述了统计信息之间的关系。

# 最有用的统计数据

所有的统计数据都被分成不同的类别。下面列出了最有用的类别。以下各节描述了每个类别中最重要的统计信息。

*   [VMStats](#af5f)—JVM 的统计信息
*   [VMMemoryPoolStats](#4cd1)—JVM 堆内存的统计信息
*   [VMGCStats](#160c)—JVM 垃圾收集的统计数据
*   [StatSamplerStats](#5aa1) —统计数据采样器本身的统计数据
*   [ResourceManagerStats](#9440) —堆监控的统计数据
*   [分区区域统计](#acb8) —分区区域的统计
*   [LinuxSystemStats](#c94f) —操作系统的统计数据
*   [DistributionStats](#d6ef) —对等请求的统计数据
*   [CacheServerStats](#f548) —客户端对服务器请求的统计
*   [CachePerfStats](#33bc) —缓存性能统计

## VMStats

**VMStats** 实例将与 JVM 进程相关的所有统计信息组合在一起，包括:

*   **fdsOpen / fdLimit** —指示从`ManagementFactory.getOperatingSystemMXBean()`提供的`UnixOperatingSystemMXBean`中检索的 JVM 中文件描述符(FD)的当前和最大数量。如果打开的文件系统数量达到限制，则会出现“*太多打开文件*”的`IOException`。
*   **processCpuTime** —表示从`ManagementFactory.getOperatingSystemMXBean()`的`UnixOperatingSystemMXBean`中获取的 JVM CPU 的处理时间。这个统计显示了 JVM 占了主机 CPU 总量的多少(参见 [LinuxSystemStats](#c94f) )。
*   **线程** —表示从`ManagementFactory.getThreadMXBean()`提供的`ThreadMXBean`中检索到的 JVM 中的线程数

## VMMemoryPoolStats

一个 **VMMemoryPoolStats** 实例将所有与 java 堆内存空间相关的统计数据组合在一起。例子包括 *CMS 老根*、 *Par 伊甸园*、 *G1 伊甸园*和 *G1 老根*。为由`ManagementFactory.getMemoryPoolMXBeans()`提供的每个`MemoryPoolMXBeans`创建一个。

*   **currentusememory**—表示 JVM 的当前堆使用情况
*   **currentMaxMemory** —表示 JVM 的最大堆使用率

## VMGCStats

一个 **VMGCStats** 实例将所有与 java 垃圾收集器相关的统计信息组合在一起。例子包括 *ConcurrentMarkSweep* 、 *ParNew* 、 *G1 老一代*和 *G1 年轻一代*。为`ManagementFactory.getGarbageCollectorMXBeans()`提供的每个`GarbageCollectorMXBeans`创建一个。

*   **收集** —表示垃圾收集的次数
*   **collectionTime** —以纳秒为单位表示垃圾收集时间。此统计数据中的峰值可能会导致成员与分布式系统断开连接，并且可能需要对已配置的堆或区域配置进行垃圾收集调优或调整(例如，添加或更改堆 LRU 回收)。

## StatSamplerStats

**StatSamplerStats** 实例将所有与统计抽样相关的统计数据组合在一起。

*   **delayDuration** —表示统计采样器线程采集的样本之间的延迟。[主机统计采样器的](https://github.com/apache/geode/blob/develop/geode-core/src/main/java/org/apache/geode/internal/statistics/HostStatSampler.java)统计线程根据*统计采样率*属性定期对统计数据进行采样。如果 statThread 在应该采样的时候没有采样， **delayDuration** 将显示一个尖峰。这通常表明存在资源问题(例如 GC 或 CPU)，有助于缩小调查的时间范围。
*   **JVM 暂停** —表示 JVM 暂停的次数。当统计样本之间的延迟大于三秒钟时，此统计值将递增。这个时间可以通过[gemfire . statsamplerdelaythreshold](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/internal/statistics/HostStatSampler.java#L66)Java 系统属性进行配置。

## 资源管理器统计

**ResourceManagerStats** 实例将所有与堆使用情况监控相关的统计信息组合在一起。

*   **heapCriticalEvents** —表示堆使用率超过临界堆百分比的次数。临界堆百分比是成员不再接受缓存操作的百分比。它是通过 resource manager*critical-heap-percentage*属性配置的。
*   **evictionStartEvents** —指示堆使用率超过驱逐堆百分比的次数。回收堆百分比是用堆 LRU 回收定义的区域开始回收的百分比。它是通过 resource manager*eviction-heap-percentage*属性配置的。

## 分区区域状态

一个 **PartitionedRegionStats** 实例将与一个分区区域相关的所有统计信息组合在一起。

*   **桶计数** —表示成员中定义的桶数
*   **primaryBucketCount** —表示成员中定义的主存储桶的数量
*   **dataStoreBytesInUse** —表示包括主存储桶和次存储桶在内的所有存储桶的条目字节数
*   **dataStoreEntryCount** —表示所有存储桶中的条目数，包括主存储桶和次存储桶

## Linux 系统状态

**linuxSystemStats** 实例将所有与 Linux 系统性能相关的统计数据组合在一起。

*   **cachedMemory** —表示从 */proc/meminfo* 中获取的 RAM 中缓存的内存量
*   **cpuActive** —表示从 */proc/stat* 中检索到的活动 CPU 百分比
*   **空闲内存** —表示从 */proc/meminfo* 中检索的主机上可用的空闲内存量。这个统计信息有助于确定可用内存量对于 JVM 堆和本机线程是否足够。
*   **loadAverage1** ， **loadAverage5** ， **loadAverage15** —表示从 */proc/loadavg* 中检索到的正在运行和等待的进程数。这些统计数据有助于确定系统上的负载对于 CPU 的数量来说是否过高。
*   **物理内存** —表示从 */proc/meminfo* 中检索到的主机上的物理内存量
*   **recvBytes** —表示从 */proc/net/dev* 中检索到的通过网络从其他成员接收的字节数
*   **recvDrops** —表示从 */proc/net/dev* 中检索到的已接收的丢弃字节数。此统计信息的非零值表示可能存在网络问题。
*   **xmitBytes** —表示从 */proc/net/dev* 中检索到的通过网络传输给其他成员的字节数
*   **xmitDrops** —表示从 */proc/net/dev* 中检索到的传输字节数。此统计信息的非零值表示可能存在网络问题。

## 分布统计

**DistributionStats** 实例对所有与对等通信和处理相关的统计数据进行分组。

*   **节点** —表示分布式系统的成员数量
*   **functionExecutionThreads**/**functionExecutionQueueSize**—指示在所有线程都在使用时，用于处理函数执行请求的名为 [functionExecutionPool](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/distributed/internal/ClusterOperationExecutors.java#L149) 的 ExecutorService 中的线程数以及超额请求队列。 **functionExecutionThreads** 统计对应于`Function Execution Processor`线程的数量(默认最大值是处理器*16 和 100 的最大值)。如果**functionExecutionQueueSize**始终大于零，那么可以通过设置 [*DistributionManager 来增加 functionExecutionPool 的最大线程数。MAX_FE_THREADS*](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/distributed/internal/OperationExecutors.java#L31) java 系统属性。关于何时以及如何使用函数执行线程的更多信息，请参见我的[文章](/swlh/threads-used-in-apache-geode-function-execution-9dd707cf227c)。
*   **highporitythreads**/**highporityqueuesize**—表示 ExecutorService 中用于处理高优先级消息(例如 [CreateRegionMessage](https://github.com/apache/geode/blob/8333d606fce4737da1e5dc846efa53f3ab035cd3/geode-core/src/main/java/org/apache/geode/internal/cache/CreateRegionProcessor.java#L311) 、 [RequestImageMessage](https://github.com/apache/geode/blob/8333d606fce4737da1e5dc846efa53f3ab035cd3/geode-core/src/main/java/org/apache/geode/internal/cache/InitialImageOperation.java#L1522) )的名为[highporitypool](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/distributed/internal/ClusterOperationExecutors.java#L127)的线程数量，以及当所有线程都在使用中时的超额请求队列。**highporitythreads**统计对应于`Pooled High Priority Message Processor`线程的数量(默认最大值为 1000)。如果**highporityqueuesize**始终大于零，那么可以通过设置 [*DistributionManager 来增加 highPriorityPool 的最大线程数。MAX_THREADS*](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/distributed/internal/OperationExecutors.java#L28) java 系统属性。
*   **partitionedRegionThreads**/**partitionedRegionQueueSize**—指示在所有线程都在使用时，用于处理分区区域消息(例如 [PutMessage](https://github.com/apache/geode/blob/develop/geode-core/src/main/java/org/apache/geode/internal/cache/partitioned/PutMessage.java) 、 [DestroyMessage](https://github.com/apache/geode/blob/develop/geode-core/src/main/java/org/apache/geode/internal/cache/partitioned/DestroyMessage.java) )和超额请求队列的名为 [partitionedRegionPool](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/distributed/internal/ClusterOperationExecutors.java#L146) 的 ExecutorService 中的线程数。**partitionedRegionThreads**统计信息对应于`PartitionedRegion Message Processor`线程的数量(默认最大值是处理器*32 和 200 的最大值)。如果**partitionedRegionQueueSize**始终大于零，那么可以通过设置 [*DistributionManager 来增加 partitionedRegionPool 的最大线程数。MAX_PR_THREADS*](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/distributed/internal/ClusterOperationExecutors.java#L70) java 系统属性。
*   **processing threads**/**overflowQueueSize**—表示 ExecutorService 中用于处理正常消息(例如 [TXCommitMessage](https://github.com/apache/geode/blob/develop/geode-core/src/main/java/org/apache/geode/internal/cache/TXCommitMessage.java) 、 [ManagerStartupMessage](https://github.com/apache/geode/blob/develop/geode-core/src/main/java/org/apache/geode/management/internal/ManagerStartupMessage.java) )的线程数量，以及当所有线程都在使用中时的超额请求队列。 **processingThreads** 统计对应于`Pooled Message Processor`线程的数量(默认最大值为 1000)。如果 **overflowQueueSize** 始终大于零，那么可以通过设置 [*DistributionManager 来增加线程池的最大线程数。MAX_THREADS*](https://github.com/apache/geode/blob/2271b7f2b7c5ff8adc1b066894a3b714975cbe0d/geode-core/src/main/java/org/apache/geode/distributed/internal/OperationExecutors.java#L28) java 系统属性。
*   **sendersTO** —表示到其他成员的传出线程拥有的(TO)连接数。只有在 *conserve-sockets* 属性设置为 false 时，才会设置此统计信息。在这种情况下，当在一个成员中处理请求的线程需要向另一个成员发送消息时，它将创建并使用到该成员的专用连接。一个例子是当处理客户端 put 请求的`ServerConnection`线程需要将值复制到次要成员时。这将导致远程成员创建一个专用的`P2P message reader`线程来处理该消息以及来自本地成员和线程的任何未来消息。这将增加本地成员中的**发送者到**统计和远程成员中的**接收者到**统计。
*   **receiversTO** —指示来自远程成员的传入线程拥有的(TO)连接数。相应的 **sendersTO** 将在远程成员中递增。该统计对应于`P2P message reader`线程的数量，并且仅在*保存套接字*属性设置为 false 时才会被设置。
*   **senderTimeouts** —表示对于*套接字租用时间*属性(默认值为 60000 毫秒)处于空闲状态并且已经关闭的传出线程拥有(TO)连接的数量。当线程拥有的连接关闭时，其对应的远程`P2P message reader`线程也将关闭。本地**发送器到**和远程**接收器到**的统计将被递减。此外，本地 **senderTimeouts** 将递增。成员之间的线程拥有的连接是按需创建的，创建成本可能很高(尤其是使用 SSL)。一旦它们被建立，只要建立它们的线程存在，它们就应该被维护。增加*套接字租用时间*(最大值为 600000 毫秒)或将其设置为零来禁用它，将有助于确保连接不会过早关闭。
*   **replyTimeouts** —表示一个成员中的线程等待来自另一个成员的回复至少 *ack-wait-threshold* 秒(默认值=15)的次数。即使超时已经发生，线程也将继续等待，直到收到回复或者远程成员离开分布式系统。该统计信息对应于日志中的一条`15 second warning`消息。
*   **replyWaitsInProgress** —表示一个成员中等待远程成员回复的线程数。零以上的统计数据表明线程被永久阻塞。
*   **接收到的可疑消息** —表示当某个成员意外离开或出现网络问题而无法联系到特定成员时，从其他成员处接收到的可疑消息的数量
*   **可疑消息** —表示当某个成员意外离开或出现网络问题导致无法联系特定成员时，发送给其他成员的可疑消息的数量

## CacheServerStats

**CacheServerStats** 实例将所有与客户端到服务器的通信和处理相关的统计数据分组。

*   **当前客户端** —表示当前连接到此服务器的唯一客户端的数量。对于长期客户端，这个统计数据应该相对平坦。
*   **当前客户端连接** —表示客户端连接到该服务器的总数。此统计信息表明执行缓存操作的客户端线程的数量。
*   **closeConnectionRequests**—表示来自客户端的关闭连接请求的数量。对于长期客户端，该统计信息是空闲客户端连接超时和关闭频率的指示器。这个统计也和**发送者到**和**接收者到**有关系。本统计中的流失也意味着那些统计中的流失。在这种情况下，Churn 意味着从客户端到服务器以及从服务器到其成员的套接字连接被关闭和重新打开。由于创建套接字连接的成本可能很高(尤其是对于 SSL)，所以这个统计值应该尽可能接近零。如果此统计数据中有大量变动，则应该增加或禁用客户端池*空闲超时*属性。默认的*空闲超时*是五秒，这通常太低了。
*   **connections time out**—此统计信息指示服务器根据池*读取超时*属性确定的客户端上超时的连接数。尽管统计数据增加了，但是处理客户端请求的`ServerConnection`线程继续处理该请求。这个统计值应该尽可能接近零。如果不是，那么应该增加客户端池*读取超时*属性。
*   **threadQueueSize** —该统计数据显示等待`ServerConnection`线程处理的客户端请求的数量。仅当 cache server*max-threads*属性设置为大于零时才适用。这个属性导致一个名为[池](https://github.com/apache/geode/blob/8333d606fce4737da1e5dc846efa53f3ab035cd3/geode-core/src/main/java/org/apache/geode/internal/cache/tier/sockets/AcceptorImpl.java#L128)的 ExecutorService 被创建。如果 **threadQueueSize** 一直大于零，那么 *max-threads* 属性应该增加。

## CachePerfStats

**CachePerfStats** 实例对所有与缓存使用相关的统计数据进行分组。

*   **cacheListenerCallsInProgress**—表示正在进行的 CacheListener 回调的数量。该统计值在零以上，表示 CacheListener 永久阻塞。
*   **cacheWriterCallsInProgress**—表示正在进行的 CacheWriter 回调的数量。零以上的统计数据表明 CacheWriter 永久停滞。
*   **loadsInProgress** —表示正在进行的 CacheLoader 回调的数量。零以上的统计数据表明 CacheLoader 被永久阻塞。

# 结论

本文展示了在解决问题时使用的一些更有用的统计数据。