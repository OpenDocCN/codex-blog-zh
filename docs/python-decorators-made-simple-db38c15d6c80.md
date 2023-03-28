# Python 装饰变得简单

> 原文：<https://medium.com/codex/python-decorators-made-simple-db38c15d6c80?source=collection_archive---------7----------------------->

## 什么是装修工？如何使用它们？什么时候使用它们？

![](img/bbc7035ab0b2204122b5aae6cf5a948a.png)

由[麦斯威尔·尼尔森](https://unsplash.com/@maxcodes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

python[decorator](https://www.codingem.com/what-are-python-decorators/)提供了一种扩展外部函数、方法或类功能的方式。

下面是一个用功能性修饰`divide`函数来检查除数不是`0`的例子。

```
@zero_guard
**def** divide(x, y):
    **return** x / y
```