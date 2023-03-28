# 谈谈线程池和池技术

> 原文：<https://medium.com/codex/talk-about-thread-pool-and-pooling-technology-994e1bc16152?source=collection_archive---------2----------------------->

池化技术的深入分析

![](img/afca179b3b8640af5f78242c6e624781.png)

在 [Unsplash](https://unsplash.com/s/photos/home?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Spacejoy](https://unsplash.com/@spacejoy?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在 Java 语言中，有两种方法可以提高程序的执行效率，一种是使用线程，另一种是使用线程池。

在生产环境中，我们通常使用后者。

为什么会这样呢？今天我们将讨论线程池的优势，以及…