# 有效的 Java:非常谨慎地实现可序列化

> 原文：<https://medium.com/codex/effective-java-implement-serializable-with-great-caution-df123eb51cdf?source=collection_archive---------2----------------------->

![](img/a61976f991134873fe04fbd6a448a21c.png)

木村浩史在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在[的最后一个主题](https://blog.devgenius.io/effective-java-prefer-alternatives-to-java-serialization-3cf14eee190)中，我们讨论了为什么我们应该避免使用 Java 中内置的序列化框架。序列化系统很大一部分是 _Serializable _ interface。这个接口表明了 Java 序列化所承诺的一些魔力。只需添加这个接口(不需要实现任何方法),突然间就有了序列化。不幸的是，这是…