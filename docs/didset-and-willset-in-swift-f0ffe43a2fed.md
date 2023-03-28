# Swift 中的 didSet()和 willSet()方法

> 原文：<https://medium.com/codex/didset-and-willset-in-swift-f0ffe43a2fed?source=collection_archive---------4----------------------->

## [法典](http://medium.com/codex)

## 了解如何在属性更改时自动触发操作。

![](img/1f3acf8e319df5e2570ed8bbb7d63224.png)

由[布莱克·康纳利](https://unsplash.com/@blakeconnally?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 Swift 中，**属性观察者**对属性进行观察。当观察到的属性改变时，它们被触发。

根据 [Swift 文档](https://docs.swift.org/swift-book/LanguageGuide/Properties.html):

> 财产观察者观察并回应…