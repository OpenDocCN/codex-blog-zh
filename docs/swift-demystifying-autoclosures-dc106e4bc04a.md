# 了解 Swift 自动关闭

> 原文：<https://medium.com/codex/swift-demystifying-autoclosures-dc106e4bc04a?source=collection_archive---------3----------------------->

## [法典](http://medium.com/codex)

## 了解神秘的@autoclosure 是什么，以及如何在 Swift 中使用它。

![](img/aac54029dd3327ac71a0b801a434f87e.png)

照片由[卡斯帕·卡米尔·鲁宾](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# Swift 中的自动关闭

autoclosure 通过将函数参数包装在闭包中来工作。autoclosure 本身不接受参数。当调用 autoclosure 时，它返回包装在…