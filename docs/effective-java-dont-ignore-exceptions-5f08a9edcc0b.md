# 有效的 Java:不要忽略异常

> 原文：<https://medium.com/codex/effective-java-dont-ignore-exceptions-5f08a9edcc0b?source=collection_archive---------5----------------------->

![](img/d40ddf329ab74317dd4489b60e195c6a.png)

由[伊恩·巴斯比](https://unsplash.com/@ian_barsby?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

一些看似显而易见但可能很诱人的事情是忽略异常。这通常是一个主要的危险信号。忽略异常是非常容易的，你只需用一个`try`和空的`catch`块包围你的异常抛出代码。

当遇到异常情况时会抛出异常，所以忽略异常会让我们自己面临问题。*有效 Java* 比喻…