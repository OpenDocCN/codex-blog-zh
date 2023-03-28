# Haskell 编程简介

> 原文：<https://medium.com/codex/introduction-to-programming-in-haskell-36572ddf22cd?source=collection_archive---------15----------------------->

## 基本语法、GHCi 和数学函数

![](img/6354ec60bc04bf515bac26dabf9c8f8c.png)

照片由 [Antoine Dautry](https://unsplash.com/@antoine1003?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Haskell 是一种纯函数式编程语言，如果您需要，这里有一篇介绍文章和安装指南:

[](/codex/starting-with-haskell-24d6027a719e) [## 从哈斯克尔开始

### 一种纯函数式编程语言

medium.com](/codex/starting-with-haskell-24d6027a719e) 

# 注释和主要功能

你会说编程语言的第一件事就是打印“Hello World”。我想说的是知道如何写评论。注释只是一些表明程序是什么或者特定代码行在做什么的文本。编译器永远不会呈现此注释；它只是为了程序员阅读和理解。

注释有两种类型:单行注释和多行注释。单行注释将以`--`开始，而多行注释分别以`{-`和`-}`开始和结束。

```
-- My first Haskell program{-
Nothing inside here will affect the progam.
I will print Hello World.
-}main = do
  putStrLn "Hello World!"
```

如果您将它保存在一个名为`main.hs`的文件中，那么要运行这个程序，您应该打开工作目录的命令提示符并运行

```
ghc --make main
./main
```

第一条命令将创建一些新文件，第二行运行生成的`main.exe`文件。您应该看到“Hello World”这一行。

**注意:**所有用 Haskell 编码的文件都以文件扩展名`.hs`结尾

# GHCi

到目前为止，在我的 Haskell 初学者之旅中，我发现 GHCi 中只有两个主要命令是有用的。第一个是加载一个模块。当您编写一个程序并用`.hs`扩展名保存它时，您可以使用交互式 IDE 来运行这个模块。

假设您有一个名为`main.hs`的文件，您可以使用以下命令将它加载到 GHCi 中:

```
:l main
```

现在，如果您已经对这个`main.hs`文件进行了修改，您可以通过简单地输入

```
:r
```

# 声明变量

在 Haskell 中声明变量非常容易。语法是:

```
<variable-name> = <value>
```

您需要确保变量名不以大写字母开头。我们通常使用 camelCase 来命名 Haskell 中的变量。

# 数学函数

## 基本功能

让我们从一个非常简单的算术运算开始

```
sumEx = 6 + 4
minEx = 6 - 4
mulEx = 6 * 4
divEx = 6 / 4
```

当您将模块加载到他们的环境中时，我们可以从 GHCi 访问这些变量。

模数运算符的处理方式略有不同。

```
modEx1 = mod 6 4
modEx2 = 6 `mod` 4
```

这两种方法都是可以接受的求两个数的模的方法。

## 高级功能

Haskell 内置了大量的数学函数。

```
piVal = pi -- this one is a variable
ePowPi = exp piVal
logOfe = log 2.71034
squared9 = 9 ** 2 -- similar to Python
sqrtOf9 = sqrt 9.0 -- note the decimal point.truncateNum = truncate 24.1
roundNum = round 1.5
ceilNum = ceiling 5.2
floorNum = floor 100.4-- Similarly we have all trigonometric functions and their hyperbolic parts.
```

我希望你喜欢读我的文章，并学到了一些东西。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile
My [Portfolio](https://cybercoder-naj.github.io) website
```