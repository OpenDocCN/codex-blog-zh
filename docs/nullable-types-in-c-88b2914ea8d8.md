# C#中可为空的类型

> 原文：<https://medium.com/codex/nullable-types-in-c-88b2914ea8d8?source=collection_archive---------15----------------------->

![](img/dc952a0af3bd30386faa4ba35ed1974d.png)

穆罕默德·拉赫马尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 C#中，我们有两种主要的类型:值类型和引用类型。值类型永远不能被赋予`null`的值。该关键字用于表示对对象的空引用。如果我们试图将`null`赋值给一个值类型，我们会得到一个编译器错误。

但是，如果我们需要表示一个可以设置为 true、false 甚至 null 的值，该怎么办呢？我们可以利用 C# *可空类型*，用于两种[值](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-value-types)类型，从 C# 8 开始，[引用](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/builtin-types/nullable-reference-types)类型，有一些关键区别(*)。

一个可为空的值类型有一个下划线类型，作为它周围的一种容器，将`null`添加到它的基本值集中。因此，如果我们声明一个可空的布尔变量，就像我们前面的例子一样，它可以被赋值为集合`{true, false, null}` *中的任何值。*

为了定义一个可空的变量类型，我们需要添加问号作为下划线类型`bool? b = null`的后缀。

向任何值类型添加问号后缀是创建通用[系统实例的一种简化方式。可空的<T>结构。因此，我们也可以使用更实质但完全等价的声明`Nullable<bool> b = null`。](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1?view=net-6.0)

该结构有两个属性: [Value](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1.value?view=net-6.0#system-nullable-1-value) ，它返回下划线类型的值(如果已经分配了一个有效的值)，以及 [HasValue](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1.hasvalue?view=net-6.0#system-nullable-1-hasvalue) ，它检查对象是否已经分配了值。我们可以用`!=`操作符执行同样的控制。它还有一个方便的[方法](https://learn.microsoft.com/en-us/dotnet/api/system.nullable-1.getvalueordefault?view=net-6.0#system-nullable-1-getvalueordefault) `GetValueOrDefault()`，可以给出下划线类型的当前值或默认值(如果为空)。

我们正在处理可能是`null`的类型，所以我们可以执行的最频繁的操作是检查`null`并相应地赋值或检索值。既然如此常见，我们可以使用一个非常简洁的简写来替代经典的 if/else 语句:空合并运算符*`??`*。**

*在第 10 行，如果我们从方法中得到一个空值，我们把 0 赋给变量。*

*除此之外，我们可以使用*空值合并赋值操作符* `??=`将值直接赋给空值变量`highscore ??= 0`。*

*C#也有一个条件操作符`?.`，类似于 Angular 中的*安全导航操作符*，在访问对象属性之前检查是否为空，而不是使用条件语句。这样，如果我们试图访问一个可能为空的对象的某个属性，如果该对象结果为空，我们不会得到运行时错误。*

*(*)可空引用类型是 C# 8 提供的一个可选语言特性。即使它们使用相同的问号后缀，也不是 System 的简写。可空<t>。</t>*