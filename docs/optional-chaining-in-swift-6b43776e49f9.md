# Swift 中的可选链接是什么

> 原文：<https://medium.com/codex/optional-chaining-in-swift-6b43776e49f9?source=collection_archive---------5----------------------->

## [法典](http://medium.com/codex)

## 了解在 Swift 中访问属性时如何处理 nil

![](img/530252e45f5f78ea12fa4454d1b87f97.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

可选链接意味着调用可选的。可选链接是安全的，如果遇到`nil`，它不会使应用程序崩溃。

例如:

```
something?.someValue?.someMethod()
```