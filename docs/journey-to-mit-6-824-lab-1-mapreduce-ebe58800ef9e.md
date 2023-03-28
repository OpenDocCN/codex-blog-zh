# 麻省理工学院之旅 6.824 -实验 1 MapReduce

> 原文：<https://medium.com/codex/journey-to-mit-6-824-lab-1-mapreduce-ebe58800ef9e?source=collection_archive---------2----------------------->

![](img/676f981b9e89aa7f666fcc377155c5f8.png)

尼克·莫瑞森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 动机

今年早些时候，我随意浏览我们公司的讨论组，碰巧看到了麻省理工学院的在线课程《麻省理工学院 6.824 分布式系统》。在我快速浏览了课程大纲后，我立刻被它吸引住了，并决定尽可能多地学习这门课程。

[https://pdos.csail.mit.edu/6.824/schedule.html](https://pdos.csail.mit.edu/6.824/schedule.html)

当时，我在日常工作中很难处理一个复杂且高度分布式的系统，我希望我能从这门课程中获得一些灵感，并对分布式系统有系统的了解，而不是一些零散的在线文章。

另一个原因是本课程中的所有实验室都使用 Golang，这是一种在云原生应用程序和开源项目(如 k8s 和 Dapr)中高度采用的语言。我认为这是我学习围棋的好机会。

现在我终于完成了 lab1 MapReduce 并通过了所有测试。我觉得分享一下我是如何做到的，我所犯的错误会很有帮助，我相信这比最终的代码更有价值。

# MapReduce 概述

现在，让我们进入主题:MapReduce 的实验 1。在我们深入研究它之前，至少你应该阅读这篇关于 MapReduce 的[论文](https://pdos.csail.mit.edu/6.824/papers/mapreduce.pdf)来获得一个基本的知识。

总结一下 MapReduce 的核心思想，通常它接受一个大的键值对集合作为输入，并生成另一个通常较小的键值对集合。

*   映射函数:每个输入键值对(k1，v1)都由一个映射函数处理。map 函数将输出(k2，v2)的列表，其中 k2 是中间密钥。
*   Reduce 函数:将每个(k2，v2 的列表)传递给 reduce 函数，reduce 函数通常将 v2 的列表合并到一个更小的值列表(v2)中作为最终输出。

# 实施 MapReduce

老实说，当我第一次克隆 lab1 框架时，我浏览了很长时间，感到困惑，我现在应该做什么。在反复阅读提示和论文后，我终于开始理解事物是如何连接起来的。

需要添加我自己的代码的所有三个文件是:

*   `src/mr/coordinator.go`添加维护状态的逻辑
*   `src/mr/worker.go`添加执行每个映射/缩减作业的逻辑。
*   `src/mr/rpc.go`添加 RPC 调用参数并回复契约。

这个实验室的总体思路很简单。在这个实验中，框架将启动一个协调器和多个工作器。当一个新的 worker 生成时，它通过 RPC 调用向协调器请求一个作业->执行接收到的作业->产生输出->报告作业结果。重复这些步骤，直到整个 MapReduce 完成。

这里要实现四个主要部分:

*   设计数据结构以保存 MapReduce 状态
*   设计协调员和工人之间的沟通
*   处理 map/reduce 工作，并产生适当的输出
*   协调者

## 数据结构

首先，让我们考虑一下协调者需要维护什么样的状态，并选择合适的数据结构来保存它们:

*   减速器的总数，记为`nReducer int`
*   地图作业的状态。一个`mapStatus map[string]int`来跟踪每个输入文件名及其状态。`mapStatus['pg-grimm.txt']=0`表示输入文件`pg-grimm.txt`还没有开始运行。
*   裁减职位的状态。一个`reduceStatus map[int]int`来跟踪每个减速器及其状态。`reduceStatus[0]=1`表示第一台减速机正在运行。
*   每个地图作业的自增量 id。每次将地图作业分配给工作人员时，此 id 都会增加 1。
*   中间文件位置。A `intermediateFiles[int][]string`记录每个减速器的中间文件。

这就是协调员要保持的所有状态。由于 works 同时与协调器对话，我们也需要一个互斥锁。`Coordinator`结构如下所示:

```
type Coordinator struct { 
    mapStatus         map[string]int 
    mapTaskId         int
    reduceStatus      map[int]int
    nReducer          int 
    intermediateFiles map[int][]string 
    mu                sync.Mutex
}
```

## 位置遥控(remote position control)

一旦我们定义了数据结构，让我们考虑一下协调者和工作者之间需要什么样的通信消息。

**请求工作:**

当一个 worker 被派生时，它通过 RPC 向协调器请求一个作业。协调器对`mapStatus`进行迭代，以找到具有以下有效载荷的第一个未分配的地图作业:

```
type MapJob struct { 
     InputFile    string 
     MapJobNumber int 
     ReducerCount int
}
```

*   `InputFile`是要打开的输入文件。
*   `MapJobNumber`被用作中间文件的一部分
*   `ReducerCount`用于决定密钥分区。

如果未找到未分配的地图作业，但仍有正在运行的地图作业，协调器会向工作器发送一个`nil`地图作业，以便工作器稍后可以再次请求，因为缩减作业只能在所有地图作业完成后开始。

如果所有地图作业都已完成，协调器将遍历`reduceStatus`以查找第一个未分配的归约作业，其有效负载如下:

```
type ReduceJob struct { 
     IntermediateFiles []string 
     ReduceNumber      int
}
```

*   `IntermediateFiles`是减速器编号`ReduceNumber`的中间文件

如果所有作业都已完成，将向工作人员发送退出信号，下面是请求作业响应:

```
type RequestTaskReply struct { 
     MapJob    *MapJob 
     ReduceJob *ReduceJob 
     Done      bool
}
```

**报告已完成的任务**

每当一个工作者完成一项工作，它就向协调者报告已完成的工作，以便协调者可以更新状态。请求负载取决于作业类型，响应负载为空。

如果作业是一个地图作业，作业应该将以下信息告诉协调者，`InputFile`是地图作业标识符，`IntermediateFile`是该地图作业产生的文件的位置。

```
type ReportMapTaskArgs struct { 
     InputFile        string
     IntermediateFile []string
}
```

如果作业是缩减作业，则作业只向协调者报告缩减者编号。

## 工人

现在是时候进入最具挑战性的部分来实现 map/reduce 作业的处理程序了。

**地图作业**

当一个工人收到一个地图作业时，它首先打开输入文件，阅读所有内容，并准备调用地图函数`mapf(filename, string(fileContent))`。注意文件名不在字数统计程序的映射函数中使用，但是`mapf`函数无论如何都需要一个键。

在`mapf`函数中，它只是将内容拆分成一个单词数组，对于每个作品，它生成一个键值对，例如{"hello "，1}，因此`mapf`函数的最终输出是一个键值对`kva`数组。

与 real MapReduce 的一个显著区别是，所有的中间数据都在一个地方`kva`，但对于 real MapReduce，我们需要将中间数据分成 N 个分区，其中 N 是 reducers 的数量。

所以，工人需要迭代`kva`。对于每个键值对(k，v)，使用提供的哈希函数来决定这个键属于哪个缩减器:

```
partitionedKva := make([][]KeyValue, reduceCount)  
for _, v := range kva {  
    partitionKey := ihash(v.Key) % reduceCount  
    partitionedKva[partitionKey] = append(partitionedKva[partitionKey], v) 
}
```

最后，用文件名`mr-x-i`将`partitionedKva[i]`持久化到磁盘，其中`x`是地图作业编号。完成此操作后，工作可以通过 RPC 报告地图作业已完成。

**减少工作**

当一个缩减器接收到一个缩减作业时，这个作业包含了需要由这个缩减器处理的所有中间文件。工人首先逐个打开所有的中间文件，构建一个键值对数组`kva`。

通常中间键值对需要排序，所以我们可以使用提供的函数来排序`kva`。排序后的`kva`如下图所示:

```
{"Key": "A", "Value": "1"}
{"Key": "A", "Value": "1"}
{"Key": "About", "Value": "1"}
```

`reducef`函数接受一个键和键的值。在字数统计程序中，工作人员需要迭代排序后的`kva`来查找同一关键字下的所有值，如下所示:

```
reducef("A", ["1", "1"]) => output A     2
reducef("About", ["1"])  => output About 1
```

每次一个`reducef`输出一个结果，我们可以把它保存到一个临时文件中，一旦所有的键都被归约，我们可以把这个临时文件重命名为最终的输出文件`mr-out-x`，其中`x`是归约器的编号。它不直接写入最终输出的原因是工人可以在任何时候崩溃，这可以防止 reducer 产生一个损坏的输出文件。

## 协调员

对于协调员来说，还有两件事要做:

1.  处理工人崩溃
2.  所有工作完成后退出。

要退出，协调器应正确执行`Done`功能。这非常简单，当所有的`reduceStatus`都完成时，只返回`true`。

工作者可以在任何时候崩溃，并且如果工作者崩溃，协调器需要将分配给崩溃工作者状态的作业更新回`NotStarted`。

为了实现这一点，我们需要跟踪一个作业被分配给一个工人的时间，协调器将生成一个 ticker 来检查是否有任何正在运行的作业超时。

此时，如果我们运行`bash test-mr.sh`，您应该会看到所有测试都已通过。

# 结论

感谢您阅读到这里。我知道这很乏味，很可能不好玩。当我完成这个 lab 1 MapReduce 时，回头看看它，它并不是非常难。关键是要理解 MapReduce 的核心思想:

*   map 函数期望什么作为输入？
*   中间输出是什么样子的？
*   reduce 函数期望什么作为输入？
*   最终输出是什么样的？

一旦你弄清楚了它们，你应该能够毫无问题地编写自己的代码。

最后，下面是我对 lab1 的粗略实现:[https://github.com/QingpingMeng/mit6.824/tree/lab1](https://github.com/QingpingMeng/mit6.824/tree/lab1)