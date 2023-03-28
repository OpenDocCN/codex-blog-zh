# 成为专业人士之前，你绝对应该学习的四个 Python 编程技巧

> 原文：<https://medium.com/codex/four-python-programming-tips-you-should-absolutely-learn-before-going-pro-7ca1ff8ecc4?source=collection_archive---------9----------------------->

![](img/bb6f2f3884a794b12c2f78efd9c8f79a.png)

[斯科特·格雷厄姆](https://unsplash.com/@homajob?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

编程的一个规则是，我们总是试图让我们的代码更易读，更简洁和干净。这里有四种方法可以让你的代码更直接，更有逻辑性。这当然是有用的，因为它避免了复杂繁琐的代码的干扰。

# ***1。条件表达式***

Python 支持可用于普通控制结构的条件表达式语法。语法的形式是-

```
expression1 **if** condition **else** expression2
```

这个复合表达式的含义是:如果条件设置为 true，则计算表达式 1；否则，计算表达式 2。这有点类似于 C++或 Java 语言中的表达式，等价于语法等价于语法， **condition？expr1 : expr2v。**下面介绍这样一个例子。它检查 n 的值。如果 n 的值大于等于 100，那么它将打印 n。否则它将打印-1。

```
param = n if n>=100 else -1
print(param)
#This would print 100
```

# 2.理解句法

理解语法可以用于我们需要根据另一个序列找到一系列值的情况。理解语法最常见的应用是**列表理解**。一般形式描述为-

```
[expression **for** value **in** iterable **if** condition]
```

这里*表达式*和*条件*都可能依赖于*值*和*，如果*子句是可选的。传统上它被描述为-

```
result = [ ] 
**for** value **in** iterable:
   **if** condition: 
      result.**append**(expression)
```

通过 list comprehension，我们可以如下定义打印数字的平方:

```
squares = [k*k **for** k **in** **range**(1, n+1)]
#It prints the square of the numbers from 1 till n+1
```

其他类似的用法包括:

```
[ k*k for k in range(1, n+1) ]   -- ***list comprehension***
{ k*k for k in range(1, n+1) }   -- ***set comprehension*** 
( k*k for k in range(1, n+1) )   -- ***generator comprehension***
{ k:k*k for k in range(1, n+1) } -- **dictionary comprehension**
```

# **3。包装和拆包顺序**

Python 提供了额外的便利，包括元组和其他序列类型的处理。如果我们有一些用逗号分隔的数列。它们将被视为单个元组，即使没有提供括号。

```
**my_list** = 2, 4, 6, 8
```

这将把 **my_list** 分配给元组(2，4，6，8)。这被称为元组的自动打包。另一种方式是当我们从一个函数返回多个值时，比如:

```
**return** maximum_val, minimum_val
#returns a single object that is the tuple (maximum_val, minimum_val).
```

反过来叫做**拆包。**它允许为序列元素分配一系列单独的标识符。举个例子:

```
**a, b, c, d** = **range**(1, 5)
#Here **a**=1,**b**=2,**c**=3 and **d**=4
```

对于这种语法，右边的表达式可以是任何可迭代的类型。值得注意的是，只要左侧变量的数量与迭代中元素的数量相同。否则，就会导致错误。

# **4。同时分配**

自动打包和解包的结合形成了一种称为同时分配的技术。在这里，我们显式地将一系列值分配给一系列标识符。比如:

```
**x,y,z** = 1,2,3
#It assigns **x**=1, **y**=2, **z**=3
```

当使用同时赋值时，在对左边的变量进行任何赋值之前，所有的表达式都在右边求值。这很重要，因为它提供了一种方便的方法来交换与两个变量相关的值:

```
**j, k** = **k, j**
```

有了这个命令， **j** 将被赋给 **k** 的旧值， **k** 将被赋给 **j** 的旧值。如果没有同时赋值，交换通常需要更精细地使用临时变量，例如:

```
**temp** = **j**
**j = k** 
**k = temp**
```

在同时赋值的情况下，在执行这种交换时，右边表示打包值的未命名元组隐式地充当临时变量。假设我们用 python 定义了一个斐波那契函数。我们首先将 a，b 的值初始化为 0，1。然后打印 a+b 的值。通过使用上述技巧，我们可以将其定义为:

```
**def** fibonacci(a):
   a, b = 0, 1
   **while** **True**:
     return a 
     a, b = b, a+b
```

我希望你能够理解这些提示和技巧，并能够在日常编码中使用它们，因为这将使你的代码脱颖而出。如果你喜欢这个帖子，请分享。