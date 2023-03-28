# Ruby Procs 和 Lambdas:有什么区别？

> 原文：<https://medium.com/codex/ruby-procs-and-lambdas-whats-the-difference-bb64225e7c13?source=collection_archive---------11----------------------->

![](img/1b85e31fa9ed6b1e85bd0dfe1b76abb9.png)

阿诺德·弗朗西斯卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## [法典](http://medium.com/codex)

Procs 和 Lambdas 的用法相似，但在特定方面有所不同。这两个函数都是高阶函数，它们将其他函数作为参数，或者将它们作为结果返回。将它进一步分解成一个块是一个无名方法，它作为参数传递给另一个方法。

块不能保存为变量。它们是 ruby“一切都是对象”规则不成立的少数例子之一。

# Procs

过程是可以绑定到一组局部变量的代码块。它们本质上是一个保存的代码块。

```
def foo(x, y, proc_block)
  proc_block.(x, y)
endadd = proc { |a, b| a + b }puts foo(3, 6, add) => result is 9
```

我们用参数`x`、`y`和`proc_block`声明一个方法`foo`。`x`和`y`将是数字，而`proc_block`将是触发。

然后我们声明变量`add`，这是一个保存为`proc`的代码块。我们用单词`proc`声明一个`proc`，然后在花括号中是我们想要运行的代码。

最后，我们把它们放在一起，输入两个值和我们的过程。结果是 9 的和！

# 兰姆达斯

兰姆达斯来自`proc`班，长得很像！然而，这两者之间存在着微小但同时又很大的差异。它们是匿名函数，所以本质上是自动执行的`proc`。你可以通过使用单词`lambda`(老派)或者使用带有`->`的‘stabby’语法来声明一个 lambda。我将重新创建上面的`proc`示例。

```
def foo(x, y)
  ->(x + y)
endx = 3; y = 6result = foo(x, y).call # or you can invoke using just .()result2 = foo(x, y).()result    #9
result2   #2
```

这里有一个关于 lambda 和 proc 是什么的简短概述！