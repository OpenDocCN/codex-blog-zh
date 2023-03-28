# 有效的 Java:记录每个方法抛出的所有异常

> 原文：<https://medium.com/codex/effective-java-document-all-exceptions-thrown-by-each-method-1c222ecc0e3e?source=collection_archive---------16----------------------->

![](img/707ec83b6af1e2edfcbd0dbf139ff709.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

文档对于快速理解一个类及其方法至关重要。异常，无论是检查的还是未检查的，都是方法契约的一部分，因此应该正确地记录下来，以便让代码的用户快速理解代码的行为。

好的文档的一部分是它非常具体。虽然这不是一个谎言…