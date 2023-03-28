# 有效的 Java！作为返回类型，首选集合而不是流

> 原文：<https://medium.com/codex/effective-java-prefer-collection-to-stream-as-a-return-type-e5c92ce3da73?source=collection_archive---------7----------------------->

![](img/ef74f87b7304bdf41f166fc0c35992cb.png)

照片由[埃米尔·佩伦](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

通常在编写方法时，我们会发现自己返回了一组项目。这可能是在运行时生成的，从持久性存储中返回，或者通过其他方法返回。使用我们新发现的流工具，我们可以从这些方法中返回一个`Stream`对象。这有时很有意义，但在其他情况下并不是正确的选择。这就是本章的内容…