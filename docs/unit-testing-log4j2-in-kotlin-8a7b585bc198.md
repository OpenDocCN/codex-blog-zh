# Kotlin 中的单元测试日志 4j2

> 原文：<https://medium.com/codex/unit-testing-log4j2-in-kotlin-8a7b585bc198?source=collection_archive---------1----------------------->

![](img/491e4dbd148bf2237c1f95029db75507.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com/) 上的照片

当对一个类进行单元测试时，我们通常会模仿或删除任何需要的依赖。如果我们记录了一条消息，单元测试的传统方法是创建一个 logger 类的模拟实现。然后，我们验证模拟记录器调用了日志记录方法。

对于 Log4j2，我们可以使用一种更简单的方法，注入一个定制的 Appender，而不是创建模拟记录器实现。