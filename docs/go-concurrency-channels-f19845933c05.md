# Go:并发+通道

> 原文：<https://medium.com/codex/go-concurrency-channels-f19845933c05?source=collection_archive---------3----------------------->

![](img/9fd66b41f4ef2eff9d45b6935cb7160e.png)

约书亚·索蒂诺在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*这篇文章是我上一篇文章* [*Go:并发性 vs 并行性*](https://emctackett.medium.com/go-concurrency-vs-parallelism-c3fc9cec55c8) *的后续。*

上面链接的前一篇文章介绍了 Go 中并发的概念。在本文中，我们将讨论通道以及如何在一个为并发性而构建的程序中利用通道。

# 什么是渠道？