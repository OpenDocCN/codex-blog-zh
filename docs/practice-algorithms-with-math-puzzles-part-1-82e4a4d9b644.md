# 用数学智力题练习算法第 1 部分

> 原文：<https://medium.com/codex/practice-algorithms-with-math-puzzles-part-1-82e4a4d9b644?source=collection_archive---------9----------------------->

## [法典](http://medium.com/codex)

## 你好，我们要解决一个关于回文数字的谜题。

![](img/aff33b19a7dd481eab303dbeaec453f4.png)

由[阿尔瓦罗·雷耶斯](https://unsplash.com/@alvarordesign?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

回文数字反过来读也一样，比如 121 或 1221。

让我们看看如何写一个算法，如果一个正整数是回文，则返回 true，否则返回 false。我用的是 python 语言。有不同的方法可以做到这一点，但我们希望提出一种具有线性时间复杂度的算法，这意味着它的运行时间应该随着输入的位数线性增加。我用的是 python 语言。我们应该检查一下这个数是否等于它的倒数。

## 方法 1: **没有**转换为字符串

while 循环创建了输入的逆过程，最好的方法是用我们的好朋友笔和纸来跟踪像 121 这样的小输入的循环。

如果 k 是输入的位数:

while 循环迭代的次数= k

第 8 行的比较次数= k

所以函数的总运行时间与 k 成线性关系。

## 方法 2:转换为字符串

```
def func(n):
    x= str(n)
    return x==x[::-1]
```

str()是一个 python 内置函数，它将给定的对象转换成字符串。

x[::-1]表示我们从最后一项到第一项对字符串进行子集化，所以给出相反的结果，但是 x[:]表示从第一项到最后一项进行子集化，所以只给出字符串本身。

## 比较运行时间

```
import time
s = time.time() # storing current time in s
func(10**10+1)  # passing it a palindrome number
e = time.time() # storing current time in e
print(e-s)      # printing the running time
```

对这两种方法都应用上面的代码让我看到方法 2 比方法 1 快。

感谢阅读。