# 有效的 Java:更喜欢接口而不是反射

> 原文：<https://medium.com/codex/effective-java-prefer-interfaces-to-reflection-73df42ebdc97?source=collection_archive---------8----------------------->

![](img/31af9ae65198526464cec53f98abd6eb.png)

法耶·康尼什在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Java 在其反射系统中提供了非常强大的功能。反射允许我们在调用类、它们的构造函数和方法时有极大的灵活性。我们与之交互的这些类在我们写自反代码的时候可能还不存在。然而，这种灵活性不是免费的；使用它也有不好的一面。其中一些如下: