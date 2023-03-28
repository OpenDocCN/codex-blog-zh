# Swift 中的 ARC 是什么

> 原文：<https://medium.com/codex/swift-understanding-arc-f518ea72e891?source=collection_archive---------7----------------------->

## [法典](http://medium.com/codex)

## Swift 使用自动引用计数(ARC)处理内存管理。通过例子了解它是如何工作的。

![](img/0a8b9787a4239e5184ded18728bb23bf.png)

由[欧文·史密斯](https://unsplash.com/@mr_vero?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 Swift 中，ARC 跟踪类实例。它会自动决定何时释放它们所使用的内存。