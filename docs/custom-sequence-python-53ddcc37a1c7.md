# 自定义序列 Python

> 原文：<https://medium.com/codex/custom-sequence-python-53ddcc37a1c7?source=collection_archive---------7----------------------->

![](img/0f2487367161a0753b9d1a76f29c2de1.png)

塔德乌什·拉科塔在 [Unsplash](https://unsplash.com/s/photos/sequence-animal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我在 Python 中用过`__str__`、`__repr__`、`__class__`、`__len__`、`__init__` 特殊/dunder/magic 方法。当我得知还有其他特殊的方法可以用来将方法的高级行为映射到 Python 内部调用时，我感到惊喜。让我们开始创建我们自己的定制序列。

我们将使用序列协议的`__getitem__`、`__len__`来创建我们自己的序列。这里的协议指的是一个契约，它基本上声明了您需要实现某些特殊的/dunder/magic 方法来使数据结构以某种方式运行。我们还将添加其他添加剂，使一个序列有更多的功能。

让我们来看一个实现协议所需的 dunder 方法的序列示例:

运行该文件时，我们得到:

```
**for looping squares**
0
1
4
9
16**squares after the for loop**
[]**reinitialising Squares...**
**using list comprehension**
[0, 1, 4, 9, 16]**Out of index error as we are trying to get 6th element from the sequence**
Traceback (most recent call last):
  File "/Users/<username>/Documents/Projects/medium/custom-sequence/squares-seq.py", line 33, in <module>
    print(f'{squares[5]}')
  File "/Users/<username>/Documents/Projects/medium/custom-sequence/squares-seq.py", line 12, in __getitem__
    raise IndexError
IndexError
```

看，你可以创建自己的列表。如果你试图访问一个不可访问的元素，你会得到`IndexError`。还要注意，一旦一个序列完成迭代，我们需要再次初始化这个类来再次迭代。

现在，让我们像内置序列类型一样添加更多的哨声和铃声，让代码变得更有趣。

运行该文件时，我们得到:

```
**original:** [Square(num=0, square=0), Square(num=1, square=1), Square(num=2, square=4), Square(num=3, square=9), Square(num=4, square=16)]**getting 3rd element:** Square(num=2, square=4)**getting element from index 2 to 4 using slice:** [Square(num=1, square=1), Square(num=2, square=4), Square(num=3, square=9)]**setting values in slice(1, 3)...**
**squares list now:** [Square(num=0, square=0), Square(num=6, square=36), Square(num=7, square=49), Square(num=3, square=9), Square(num=4, square=16)]**deleting 3rd element from square...**
**squares list now:** [Square(num=0, square=0), Square(num=6, square=36), Square(num=3, square=9), Square(num=4, square=16)]
```

在这里，我们指导了 dunder 方法如何表现，以便它像 Python 的内置序列`list`一样工作。

我们实现了`__setitem__`、`__getitem__`、`__delitem__`，这样我们就可以设置、获取和删除序列中的项目。

我们还有其他方法来调用这些 dunder 方法。例如:`__setitem__`的赋值操作:

```
squares[2] = 10
```

`__getitem__`的括号查找:

```
squares[2]
```

`del`操作员为`__delitem__`:

```
del squares[2]
```

你还可以实现许多其他方法，比如`append`、`extend`、`clear`、`pop`、`__add__`、`__iadd__`来使你的`Squares`序列更加有效。

这个练习感觉非常类似于我在本科上的算法课，你基本上实现了你自己的自平衡树，Splay 树。

本文到此为止。希望它给你更多的工具来创建你自己的自定义数据结构，除了 Python 的内置类型！

恭喜你！💃下周我们将主要讨论 Python 中的 Iterable vs Iterator 或者`__delitem__` vs `__delete__` vs `__del__`。

灵感:

*   [巨蟒深潜](https://www.udemy.com/course/python-3-deep-dive-part-2/)

你可以在 [Patreon](https://www.patreon.com/dkhambu) 支持我。