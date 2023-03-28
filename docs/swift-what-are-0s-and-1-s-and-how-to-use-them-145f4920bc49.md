# Swift 中的 0 美元和 1 美元是什么

> 原文：<https://medium.com/codex/swift-what-are-0s-and-1-s-and-how-to-use-them-145f4920bc49?source=collection_archive---------1----------------------->

## [抄本](http://medium.com/codex)

## 和钱有关系吗？

![](img/b6b29eeaa3a31f75560d9787fcb07b3c.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

`$0`、`$1`等表示对集合中的项目执行动作时的项目。

例如，要打印一个数字列表，可以使用内置的`forEach()`函数和`$0`，如下所示:

```
**let** numbers = [1, 2, 3, 4, 5]
numbers.forEach { print($0) }
```