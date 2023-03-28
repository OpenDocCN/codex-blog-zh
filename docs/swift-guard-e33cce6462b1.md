# Swift Guard 关键字—我应该在什么时候使用它？

> 原文：<https://medium.com/codex/swift-guard-e33cce6462b1?source=collection_archive---------12----------------------->

## 了解如何用 guard 语句替换 if 语句。

![](img/aefe2be25308da9a5c2e2a94b81684d1.png)

照片由[维普·贾](https://unsplash.com/@lordarcadius?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Swift 的`guard`关键字可以替代 if 语句，提高你的代码质量。`guard`防止你的应用因为无效数据而崩溃。

`guard`语句的工作原理是，当一个或多个条件不满足时，将程序控制转移到范围之外。