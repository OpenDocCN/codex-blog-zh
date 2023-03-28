# Swift:关键路径基础

> 原文：<https://medium.com/codex/swift-key-path-basics-b4ac22cb90c5?source=collection_archive---------5----------------------->

## [法典](http://medium.com/codex)

## 了解什么是关键路径以及如何使用它们。

![](img/894c5478997dd69e1a348132395e64ec.png)

由[约书亚·雷德科普](https://unsplash.com/@joshuaryanphoto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# Swift 中的关键路径

关键路径提供了一种动态且类型安全的引用类型属性的方式。

```
KeyPath<ObjectType, PropertyType>
```

键路径是对属性的引用，而不是对属性值的引用。