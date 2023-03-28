# 实现最佳性能:节点速度提高 10 倍。带有工作线程的 Js

> 原文：<https://medium.com/codex/achieve-the-best-performance-10x-faster-node-js-with-worker-threads-97fc2890e40f?source=collection_archive---------4----------------------->

![](img/4689014141c7fd8107404352299f8901.png)

保罗·史密斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## 使用不同的工作线程配置应对高度密集的 CPU 操作吞吐量。

我最近才知道，让 JavaScript 并行执行是有可能的，我给了 Node。JS [工人线程](https://nodejs.org/api/worker_threads.html)一试，我对我得到的结果感到兴奋。