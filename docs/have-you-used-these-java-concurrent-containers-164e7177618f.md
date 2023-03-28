# 你用过这些 Java 并发容器吗？

> 原文：<https://medium.com/codex/have-you-used-these-java-concurrent-containers-164e7177618f?source=collection_archive---------10----------------------->

![](img/93421eaef6481fb281ccaf4f31f7a4e3.png)

由 [kazuend](https://unsplash.com/@kazuend?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

相信很多小伙伴在很多并发场景下，经常会用到 ConcurrentHashMap、ArrayBlockingQueue 等线程安全的容器类。虽然这些会牺牲一些效率，但在安全性上是可以保证的。细心的朋友应该能发现，上面例子中的线程安全容器都在 java.util.concurrent 包下。其实有很多…