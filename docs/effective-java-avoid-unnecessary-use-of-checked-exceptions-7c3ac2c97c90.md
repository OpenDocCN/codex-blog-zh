# 有效的 Java:避免检查异常的不必要使用

> 原文：<https://medium.com/codex/effective-java-avoid-unnecessary-use-of-checked-exceptions-7c3ac2c97c90?source=collection_archive---------18----------------------->

![](img/fc58035c67c43465ecffb1ea17b56481.png)

杰西卡·路易斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

许多开发人员不喜欢检查异常。*有效 Java* 认为不需要这样。被检查的异常的定义特性是通过捕获异常或通过签名将异常向上传递到调用堆栈来强制调用方处理异常。