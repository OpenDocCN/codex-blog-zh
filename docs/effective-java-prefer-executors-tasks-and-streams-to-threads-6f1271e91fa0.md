# 有效的 Java:比起线程，更喜欢执行器、任务和流

> 原文：<https://medium.com/codex/effective-java-prefer-executors-tasks-and-streams-to-threads-6f1271e91fa0?source=collection_archive---------3----------------------->

![](img/13c76968b8fb6b0cd96a23c9e6d0eabc.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [amirali mirhashemian](https://unsplash.com/@amir_v_ali?utm_source=medium&utm_medium=referral) 拍摄的照片

最终，似乎每个开发人员都会遇到一个需要某种*工作队列*的问题。这些工作队列可以用来存储一组任务，这些任务按照一定的顺序进行处理，然后继续前进。尽管这些工作队列的概念可能很简单，但实际上以一种安全的、高性能的方式开发其中的一个可能很棘手并且容易出错。谢天谢地，我们有…