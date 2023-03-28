# Python 程序的 5 个高级代码片段

> 原文：<https://medium.com/codex/5-advanced-code-snippets-for-your-python-programs-3377baa3d544?source=collection_archive---------1----------------------->

## 使用它们，让人们惊奇

![](img/2b2fa09f0582ef4987f298b44c3e9c82.png)

乔·耶茨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

今天，我将与您分享《Python 技巧:这本书》中的一些片段。这本书有很多关于我们日常使用的一些片段的深入信息，帮助你理解它们是如何工作的。

我建议你读完这篇文章后自己看看。

# 1.使用 __repr__ & __str__ 方法可以更好地表示类对象

```
>>> class Glass:
...     def __init__(self, reflct):
...             self.reflct = reflct
...>>> Glass('MIRROR')>>> glass = Glass('MIRROR')
>>> glass#OUTPUT
**<__main__.Glass object at 0x0000028D48967550>**>>> print(glass)#OUTPUT
**<__main__.Glass object at 0x0000028D48967550>**
```

对象的字符串表示没有被覆盖，这就是你看到这个的原因。

```
>>> class Glass:
...
...     def __init__(self, reflct):
...         self.reflct = reflct
...
...     def __str__(self):
...         print('inside __str__ method')
...
...     def __repr__(self):
...         print('inside __repr__ method')
...>>> glass = Glass('MIRROR')# This will all statements will use class __str__ method
>>> '{}'.format(glass)
>>> str(glass)
>>> print(glass)#OUTPUT
inside __str__ method# This will use __repr__ method
>>> glass#OUTPUT
inside __repr__ method
```

# 2.在 Python 中克隆对象

在 Python 中，赋值语句不创建对象的副本，它们只将名称绑定到一个对象。

## 浅拷贝

它创建一个新的集合对象，然后用在原始。因此，不会创建子对象本身的副本。

```
a = [[1, 2, 3], [4, 3, 5]]
b = list(a) # makes a shallow copy
a[0][0] = 'x'#OUTPUT
**Before**
[[1, 2, 3], [4, 3, 5]]
[[1, 2, 3], [4, 3, 5]]
**After**
[['x', 2, 3], [4, 3, 5]]
[['x', 2, 3], [4, 3, 5]]
```

## 深层拷贝

它创建一个新的集合对象，然后用原始集合中找到的子对象的副本递归地填充它。这样，它会创建原始对象及其所有子对象的完全独立的克隆。

```
import copya = [[1, 2, 3], [4, 3, 5]]
b = copy.deepcopy(a)a[0][0] = 'x'#OUTPUT
**Before**
[[1, 2, 3], [4, 3, 5]]
[[1, 2, 3], [4, 3, 5]]
**After**
[['x', 2, 3], [4, 3, 5]]
[[1, 2, 3], [4, 3, 5]]
```

# 3.内嵌列表理解

以 Pythonic 方式填充列表的最佳方式是只添加表达式和循环。

如果您是 c 或 c++开发人员，这种一行程序一开始很难理解，但是在 Python 中，一旦您习惯了这种语言，一行程序就非常易读。

```
values = [expression for item in collection]
```

# 3.获取列表中第 N 大或第 N 小的元素

这个代码片段在日常场景中可能没什么用，但是有时候你可能需要列表中第 N 个最大/最小的元素，你可能认为你必须从头开始编写逻辑，但是害怕没有内置模块来拯救你。

```
import heapq

scores = [1, 10, 11, 2, 5, 6]

print(heapq.nlargest(3, scores))  # [11, 10, 6]
print(heapq.nsmallest(5, scores))  # [1, 2, 5, 6, 10]
```

# 4.像专业人士一样使用装修工！

现在，在深入装饰者的概念之前，将是一个极好的时机来刷新您对 Python 中的*一级*函数的属性的记忆。

[](https://python.plainenglish.io/you-would-have-never-used-function-this-way-32374b7df8ba) [## 你能用 Python 函数做什么

### Python 函数太疯狂了！

python .平原英语. io](https://python.plainenglish.io/you-would-have-never-used-function-this-way-32374b7df8ba) 

Decorators 一开始很复杂，但却是非常有用的特性，你会在第三方框架和 Python 标准库中经常遇到。

Decorators 允许您定义可重用的构建块，这些构建块可以改变或扩展其他功能的行为。

```
def uppercase(func):
    def wrapper():
        originalResult = func()
        modifiedResult = originalResult.upper()
        return modifiedResult
    return wrapper[@uppercase](http://twitter.com/uppercase)
def greet():
    return 'Hello World!'print(greet())# OUTPUT 
HELLO WORLD!
```

# 5.链式映射:将多个字典作为单个映射进行搜索

*系列。ChainMap* 数据结构将多个字典组合成一个映射。

查找逐个搜索底层映射，直到找到一个键。

插入、更新和删除仅影响添加到链中的第一个映射。

```
from collections import ChainMap
d1 = { 'a': 1, 'b': 2 }
d2 = { 'c': 3, 'd': 4 }
chain = ChainMap(d1, d2)# ChainMap searches each collection in the chain
# from left to right until it finds the key (or fails):
print(chain)# OUTPUT 
ChainMap({'a': 1, 'b': 2}, {'c': 3, 'd': 4})
```

如果您喜欢这篇文章，并且想了解更多关于 Python 的内容，请查看这些文章。

[](https://python.plainenglish.io/python-functions-that-make-life-easier-9d0c41b986a8) [## 让生活更简单的 5 个 Python 函数

### 让生活更简单的 Python 函数列表。

python .平原英语. io](https://python.plainenglish.io/python-functions-that-make-life-easier-9d0c41b986a8) [](https://python.plainenglish.io/if-you-are-using-assert-you-must-read-this-7256e46dc816) [## 停止使用 Assert 进行数据验证

### 对于那些使用断言操作进行数据验证的人来说，这是一篇必读的文章。

python .平原英语. io](https://python.plainenglish.io/if-you-are-using-assert-you-must-read-this-7256e46dc816) [](https://python.plainenglish.io/top-python-guides-books-github-repositories-a397a59a4f52) [## 顶级 Python 指南、书籍和 GitHub 知识库

### 学习 Python 的指南、书籍和 GitHub 库列表。

python .平原英语. io](https://python.plainenglish.io/top-python-guides-books-github-repositories-a397a59a4f52) 

你可以在我的个人资料中找到更多这样的内容。

[](/@rahulbhatt1899) [## 拉胡尔·布哈特-中等

### 阅读媒体上的拉胡尔·布哈特作品。Python，JS，满足好奇心的编码打包文章。少数游戏…

medium.com](/@rahulbhatt1899)