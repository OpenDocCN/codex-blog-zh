# 分布式系统中两阶段提交和优化

> 原文：<https://medium.com/codex/2-phase-commit-and-optimizations-in-distributed-system-9d802423b9f?source=collection_archive---------4----------------------->

## [法典](http://medium.com/codex)

![](img/46393ed1ad7418fd2ec9f4375fc0c3e8.png)

在分布式系统中，我们关心一些事情。在进入实际的算法之前，让我们先讨论这些话题。

# 可串行化与可线性化

可序列化性适用于一组事务，一次一个(即使实现可能…