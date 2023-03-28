# 让我们来谈谈 Java/JVM 标志

> 原文：<https://medium.com/codex/lets-talk-about-java-jvm-flags-23fe0f826bc2?source=collection_archive---------4----------------------->

你为什么想知道这个？为了能够调优应用程序并识别性能问题，了解应用程序实际使用了哪些标志以及可能的原因是很重要的。

![](img/2b7f992e5aee5e455c165f0c32e0282c.png)

**TL；你可以用多种方式查看你的 java 应用程序中使用的标志。要么通过在命令行上指定一个标志(-XX:+PrintFlagsFinal)，要么通过在活动虚拟机上使用 **jcmd** 或 **jinfo** 。**

我正在运行 OpenJDK 13，如果您得到不同的结果，请参考您的特定 JVM 实现文档。这里写的大部分内容也适用于其他 java 版本。

## 流程 ID

大多数监控活动 JVM 的命令都要求您识别应用程序的进程 id (PID)。检索 PID 的一个简单方法是使用 JPS [1],但是如果需要，您也可以从代码中检索它。在这里，我启动了一个简单的程序，名为 JavaFlags，它会永远循环下去。

```
> jps
...
1966 JavaFlags
...
```

如果您想通过代码检索它，可以在下面的例子中进行一个循环，这个例子说明了如何获得当前进程和其他用 java 命令启动的进程的 PID。

它提供了以下输出。

```
current process PID: 2016
PID        name      
---------- ----------
2016       PidFromCode
...
1966       JavaFlags
```

既然我们已经获得了 PID，我们就能够使用其他 java 工具来获得关于正在运行的 JVM 的更多信息。本文将讨论如何确定向 JVM 提供了什么参数以及从哪里提供的。未来的文章将讨论如何实际监控和调查可能的问题，并调优应用程序。

# 查看标志

事实上，有多种方法可以检索活动 JVM 的标志。

***旁注*** 使用 jvm 标志可以告诉 JVM 打印它在启动时使用的标志，但是这里我们假设没有提供这个标志。我把它收录在下面，供感兴趣的人参考。

```
> java -XX:+PrintFlagsFinal ...
```

要获得关于这个 JVM 及其标志的信息，可以运行下面的命令。

```
> java -XX:+PrintFlagsFinal -version
```

## **显示全部**

以下命令使用活动 VM [2]的 **jcmd** 打印与前述标志相同的信息。

```
> jcmd {PID} VM.flags -all
2636:
[Global flags]
...
bool BranchOnRegister = false              {C2 product} {default}
bool C1OptimizeVirtualCallProfiling = true {C1 product} {default}
...
uintx InitialCodeCacheSize = 2555904       {pd product} {default}
size_t InitialHeapSize = 402653184         {product} {ergonomic}
...
uintx MaxHeapFreeRatio = 70                {manageable} {default}
size_t MaxHeapSize = 10737418240           {product} {command line}
...
size_t SoftMaxHeapSize = 6442450944        {manageable} {ergonomic}
```

第一个**括号可以包含许多不同的值，下面是一些最常见的值。**

*   ***产品*** :该标志的默认值在所有平台上都是一样的，换句话说，它是一个产品构建的默认值。
*   ***pd 产品*** :该标志的默认值依赖于平台(如 linux-x64)
*   ***可管理*** :该标志的值可以在运行时设置。例如使用 **jinfo** ，如下所述【3】。
*   ***C1|C2 产品*** :该标志的默认值是针对 C1(客户端)和 C2(服务器端)JIT 编译器的。这些编译器之间的区别超出了本文的范围。简而言之，客户端编译器比 C2 运行得更快，但产生的优化代码更少。使用哪一个取决于您的平台，但在某些情况下可以被覆盖，在今天的大多数机器上，两者都被使用，因为自 java 8 以来，分层编译是默认的。
*   ***C1|C2 诊断*** :该标志提供诊断信息，不用于调谐。
*   ***ARCH product*** :该标志只对某些架构存在，对其他架构不变。
*   ***lp64_product*** :该标志只存在于 64 位 JVM，对于 32 位版本是不变的。
*   ***商用*** :该标志只适用于商用版。
*   ***实验*** :如果使用-XX:+unlockeexperimentalvmoptions 指定实验模式，则标志可用

**第二个**支架可以是下列之一。请注意，在以前的版本中，这个括号不存在，而是在标志有一个非默认值且等号前有一个冒号(":= ")时指定。

*   ***{默认值}*** :该标志的值是该 JVM 版本的默认值。
*   ***{人机工程学}*** :该值根据 JVM 设置为最佳值，并考虑了运行该值的特定机器(例如，操作系统、32/64 位或 CPU 数量)。
*   ***【命令行}*** :在命令行上设置了标志，该标志始终优先(如果多次指定了标志，则使用最后一次出现的标志)。
*   ***【attach】***:标志在运行时被改变，只适用于可管理的标志。

## 显示特定标志

您还可以像这样使用 jinfo 查看一个标志的当前值。

```
> jinfo -flag SoftMaxHeapSize {PID}
-XX:SoftMaxHeapSize=2000> jinfo -flag HeapDumpBeforeFullGC {PID}
-XX:+HeapDumpBeforeFullGC
```

# 设置标志

设置标志最常见的方式是在命令行上。语法根据它是布尔值还是值标志而有所不同。

**布尔例子**

```
> java -XX:+BranchOnRegister (enables)> java -XX:-BranchOnRegister (disables)
```

**值示例**

```
> java -XX:SoftMaxHeapSize=2G (2GB)> java -XX:SoftMaxHeapSize=2M (2MB)> java -XX:SoftMaxHeapSize=2K (2KB)
```

## 在运行时设置标志

您还可以在运行时设置一些标志(如果它们被指定为可管理的)。首先按照上面的说明获取 PID，然后可以运行下面的命令。如果你试图设置一个不可管理的标志，你会得到一个错误(在早期版本中，我相信这是被忽略的)。

**布尔示例**

```
> jinfo -flag +HeapDumpBeforeFullGC {PID} (enables)> jinfo -flag -HeapDumpBeforeFullGC {PID} (disables)
```

**数值示例**
注意，这里不能使用 K、M 或 G 等单位，而必须指定准确的数值。

```
> jinfo -flag SoftMaxHeapSize=2048 {PID} (sets it to 2M)
```

最后，有些标志有别名，可以由或指定。例如设置最大堆大小。

```
> java -Xmx3G -XX:+PrintFlagsFinal -version | grep MaxHeapSize
size_t MaxHeapSize = 3221225472 {product} {command line}
...> java -XX:MaxHeapSize=3G -XX:+PrintFlagsFinal -version | grep MaxHeapSize
size_t MaxHeapSize = 3221225472 {product} {command line}
...
```

唯一的问题是 jinfo 不能识别其中一个 alises，对于上面使用 Xmx 的情况，你会得到一个 not found 错误。

```
> jinfo -flag Xmx {PID}
no such flag 'Xmx'
```

关于这个和其他标志的更多信息可以在[这里](https://docs.oracle.com/en/java/javase/13/docs/specs/man/java.html)和[这里](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html)【4，5】找到。

**参考文献**

[1] Java 虚拟机进程状态工具(JPS)
[https://docs . Oracle . com/javase/7/docs/technotes/tools/share/JPS . html](https://docs.oracle.com/javase/7/docs/technotes/tools/share/jps.html)

jcmd 命令
[https://docs . Oracle . com/en/Java/Java se/13/docs/specs/man/jcmd . html](https://docs.oracle.com/en/java/javase/13/docs/specs/man/jcmd.html)

[3]金佛在此补充参考

[4]Java 命令
[https://docs . Oracle . com/en/Java/javase/13/docs/specs/man/Java . html](https://docs.oracle.com/en/java/javase/13/docs/specs/man/java.html)

[5] Java HotSpot VM 选项
[https://www . Oracle . com/Java/technologies/javase/VM Options-JSP . html](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html)