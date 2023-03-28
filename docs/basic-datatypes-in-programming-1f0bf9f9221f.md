# 编程中的基本数据类型

> 原文：<https://medium.com/codex/basic-datatypes-in-programming-1f0bf9f9221f?source=collection_archive---------10----------------------->

*字符串、整数、布尔和数组*

![](img/5fa03d50ccf5150073252ffb1f51b441.png)

由 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [AltumCode](https://unsplash.com/@altumcode?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

如果您是编程新手，那么所有不同的数据类型可能会令人望而生畏。有很多术语你可能以前没有听说过，或者不知道它们与编程有什么关系。

在本文中，我将尝试解释您在编程学习中会遇到的基本数据类型，并解释得足够好，让您能够理解，但仍然很容易理解。享受吧。

# 用线串

字符串是编程中最基本的类型之一。你可以把它们想象成一个字符列表。或者更简单地说，它们只是纯文本。

通过使用引号表示它是一个字符串，将它们赋给变量。至少在大多数编程语言中是这样。

```
your_variable := "your string"
```

你会经常在编程课程中看到字符串被用来写“Hello World ”,你可以说，这是你的第一个应用程序的传统。

字符串也可以被**连接**。这意味着你可以把两个字符串加在一起成为一个字符串。这通常用加号来表示。

```
string1 := "Hello "
string2 := "World"
string3 := string1 + string2
print(string3) // Prints "Hello World"
```

一些语言允许对字符串进行更复杂的操作，但是我们不会深入讨论它们。字符串在这个层次上并不是很复杂，我们目前不需要深入研究。让我们进入下一个数据类型。

# 整数

整数或`int`是数字。很简单地说至少。它们的范围通常从大约负 2.1 磅到正 2.1 磅。这是因为它们的存储方式。一些语言可能有其他的数据类型，比如用于更大数字的`long`。通常，这并不重要。

默认情况下，您总是可以对它们执行常规的数学运算。他们也经常没有任何引用或其他标记。

```
num1 := 6
num2 := 12
num3 := num1 + num2 // = 18 
num4 := num2 - num1 // = 6
num5 := num2 / num1 // = 2
num6 := num1 * num2 // = 72
```

关于整数的事情是，在大多数静态类型的语言中，如 C#或 Java，它们不能有小数点。取而代之的是 T2。

## 漂浮物

float 这个名字来自浮点数学，其中一个数字包含一个小数点。它们的声明方式因语言而异，所以我不会深入探讨。最常见的是，你必须在给变量赋值之前使用`float`关键字，并在数字后面加上一个`f`。比如`float your_var := 3.14f`。

# 布尔运算

布尔似乎是最基本的数据类型，它可以是`true`或`false`。但是进入真正的布尔逻辑和数学会很快变得困难。但是我们不讨论这个。

在大多数语言中，它们是用`true`或`false`关键字声明的。一些静态类型的语言也需要在前面加上`bool`。

```
my_var1 := true
my_var2 := false
```

复杂性来自于需要布尔值来选择路径的条件语句。最常见的是 if 语句。

```
my_var := true
if my_var == true:
    do_something()
else:
    do_something_else()
```

这些可以非常简单，或者它们可以具有多个需要相同的布尔值(AND)，或者只有一个值需要为`true` (OR)。

有了这些，你可以创建复杂的布尔逻辑，你可能会遇到以后写程序。

# 数组

数组，也称为列表，根据您使用的语言有很大的不同。但它们几乎总是有一些相似之处。它们有时可以包含单一的数据类型，也可以是动态的，包含您想要的所有类型。

```
my_list := [1, 3, 5, 7]
my_other_list := ["a", "b", "c"]
```

它们通常用括号`[]`表示，数值之间用逗号分隔。通过使用您想要访问的值的`index`访问列表，您可以随时访问每个值。

```
my_list := ["a", "b", "c"]
my_val := my_list[1] // "b"
```

列表几乎总是从 0 开始索引，这意味着列表的第一项将从 0 开始。

对于一些开发人员来说，数组可能很难完全掌握，但是通过足够的练习，您将很快掌握它们。

# 结论

一开始编程可能真的很难，但只要慢慢来，学习开发的每一个微小部分，你就能成为一名伟大的程序员。

非常感谢您的阅读，祝您度过美好的一天。

[考虑通过获得中级会员来支持我](https://mbvissers.medium.com/membership)。它帮了我大忙，不会花你任何额外的费用，而且你可以阅读尽可能多的中等文章！

在 [Twitter](https://twitter.com/0xmbvissers) 和 [gm.xyz](https://gm.xyz/u/mbvissers.eth) 上关注我，以跟上我的项目。

看看 NFT 项目中的袋鼠黑帮。

检查多边形上的[像素披萨](https://www.pixel-pizzas.com/)。