# 为什么应该学习函数式编程:类型类与接口

> 原文：<https://medium.com/codex/why-you-should-learn-functional-programming-type-classes-vs-interfaces-9d2192d20ac2?source=collection_archive---------1----------------------->

![](img/e5e5360fd51cab4e67a243dd046fb1b4.png)

图片来自 [Pixabay](https://pixabay.com/ru/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=6004000) 的[维基·汉密尔顿](https://pixabay.com/ru/users/flutie8211-17475707/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=6004000)

在这篇文章中，我将描述一个很酷的函数式编程概念**，类型类**，以 Haskell 编程语言为例。我将首先提供一些 Java 示例，并介绍 Java 语言及其面向对象范例的局限性。然后，我将描述在进入函数式编程世界时，这些限制是如何被解除的。

本文主要面向 Java 开发人员，但是任何面向对象语言的知识都可以。在阅读本文之前，您不需要有任何 Haskell 方面的经验。然而，如果你在读完这篇文章后对这种奇妙的语言或者函数式编程感兴趣，我会很高兴。这可能会帮助你对你错过的东西有一个更全面的了解，并且更好地理解不同编程语言中的类型是如何工作的。

# Java 中的接口及其局限性

接口是 Java 中无处不在的概念。您定义一组函数签名，然后在特定的类定义中实现它们:

但是如果你想要一个更抽象的界面呢？例如，有许多类型具有以下三个属性，这些属性需要提取到接口中:

首先，**某些类型的值可以追加得到另一个相同类型的值**:

*   两个字符串可以连接起来得到一个新的字符串；
*   两个整数可以相加得到一个新的整数值；
*   两个 lambda 函数可以使用`Function.compose()`来组合，这给了我们另一个函数——两者的组合。

第二，**所有这些操作都是关联的**:如果你连续组合了两个以上的值，你可以用括号改变组合的顺序，这不会影响结果:

*   `1 + (2 + 3)`与`(1 + 2) + 3`相同——都等于`6`；
*   `("hello" + " beautiful ") + "world"`与`"hello" + (" beautiful " + "world")`相同——都等于`"hello beautiful world"`
*   `fun1.compose(fun2.compose(fun3))`与`fun1.compose(fun2).compose(fun3)`相同——这只是连续应用于彼此输出的三个函数。

第三，**有一个特殊的价值，当它与另一个价值结合时，不会改变它。**姑且称之为“空”值 **:**

*   `0 + 1`和`1 + 0`一样只是`1`；
*   `"" + "hello"`和`"hello" + ""`一样只是`"hello"`；
*   `fun1.compose(Function.identity())`和`Function.identity().compose(fun1)`一样，只是`fun1`。

我们看到了很多共同点，那么我们能让所有这些类型都实现某种接口吗？在代数中，**一个具有二元运算和一个具有上述性质的特殊值的集合被称为“幺半群”**，那么我们为什么不为所有这些类型声明一个`Monoid`接口呢:

`empty()`方法返回特殊的“空”元素，而`append()`方法将这个值与另一个值组合起来，并返回一个新的组合值。如果我们可以在标准库中改变它的定义，下面是`String`类型的实现:

整洁！现在，我们可以编写通用代码，可以与任何幺半群一起工作，不管它们的真实类型是什么。例如，我们可以编写一个方法，使用`Stream.reduce()`将多个相同类型的幺半群值追加到一个中:

注意，reduce 方法的这个变体使用第一个列表元素作为初始值，然后使用`append`将所有其他值附加到它上面。这就是为什么该方法返回`Optional<T>`——当列表为空(我们没有任何值用作初始值)时，这是必需的，所以在这种情况下，该方法将返回`Optional.empty()`。

我们可以使用`empty()`值作为初始值，也可以覆盖列表为空的情况，但是我们不能调用`empty()`方法，因为它是一个实例方法，如果列表为空，那么我们没有任何实例可以调用它。

对于一列`Integer`值，这个方法会将它们相加。对于字符串，它会将一系列字符串串联起来。对于函数，它会将一组函数组合成一个大函数。看起来**我们可以写一段简单的通用代码，对很多不同的情况都有用**。然而…

# 为什么这在 Java 中不起作用

首先，您不能将接口的实现添加到一些现有的类型中。在撰写本文的时候(Java 17 就在眼前)**您只能在类型定义中指定实现的接口**，这让我们不得不等待 Java 18、19、20……或者永远等待。

第二，更概念性的问题:**有不同的方式来表示同一个幺半群**。这里至少有两种处理整数的方法，一种是加法，我们已经讨论过了:

这是另一个乘法，也有同样的性质:乘法是结合的，有一个“空”元素(即 1)在乘法后不会改变另一个值:

现在我们完全不走运了。我们不仅不能为现有类型添加一个接口，而且不能为同一类型添加一个以上的接口实现。Java 就是不允许。我们不得不求助于委托(或包装)模式，这在 Java 中通常很难看。

# 在 Haskell 中如何使用类型类

Haskell 是一种函数式语言，不是面向对象的。您可以定义您的数据类型，但是如果您将它们与 Java 类进行比较，它们更像是记录——它们只有数据(字段)而没有功能(方法)。下面是我们如何使用 Haskell 的记录语法定义一个包含三个字段的类型——姓名、姓氏(字符串)和年龄(整数值)。

但是如果类型不包含任何逻辑，我们如何抽象不同的类型来处理它们呢？我们定义类型类。类型类是一组函数签名，看起来非常类似于 Java 中的接口。

让我们看看如何使用 type 类语法在 Haskell 中定义我们的`Monoid`接口(来自 Haskell 库的实际定义稍微复杂一点，但想法是相同的):

Haskell 中的`class`关键字不像 Java 那样定义一个新类——在这里，它定义了一个新的类型类。暂时把它当做一个接口。

在上面的定义中，我们使用`class`关键字来定义一个带有一个类型变量 m 的类型类(这就是你在 Java 中写的`<T>`)。它定义了两个函数:`mempty`，返回类型参数`m`的值；以及`mconcat`，接受类型`m`的两个值，并产生第三个相同类型的值`m`。

这个字符串`m -> m -> m`可能看起来很奇怪，但它只是由`->`连接的一系列类型变量，这是 Haskell 的函数类型签名语法——字符串中的最后一个类型总是返回类型，其他都是函数参数。它看起来像是有原因的，但我们不会在这里深入这个话题。

很容易看出这个类型类如何对应于我们上面定义的 Java 接口，其中`mempty`对应于`empty`而`mappend`对应于`append`:

在 Java 中，我们说**一个类实现了一个接口。在 Haskell 中，我们说**对于这个类型**有一个类型类的实例。一个重要的区别是我们可以在类型已经被定义之后定义一个类型类的实例。**

那么，类型类如何解决接口的第二个问题——一个类型只能有一个接口实现？让我们以整数的加法和乘法为例，在这里你可以有两个不同的`Monoid`接口的实现，但是在 Java 中你不能轻易做到这一点。为了了解 Haskell 是如何做到的，让我们看看类型`Sum`和`Product`的定义:

你可以看到我们在这里使用了不同的关键字来定义一个类型— `newtype`。您可以将它视为现有类型的轻量级包装器——一旦它在编译时进行了类型检查，就会在运行时使用原始类型。`Sum`类型有一个返回包装值的属性`getSum`。`Product`也是如此。

我们可以简单地将整数值包装成这些类型:

但是这些包装纸有什么特别的吗？**它们都有一个** `**Monoid**` **类型的类实例为它们定义，根据乘法和加法的属性。**

这里是`Product`包装器的`Monoid`类型类定义的简化版本。本质上，我们说`Product`值可以被视为一个`Monoid`，并为其提供了`Monoid`函数的实现:

关键字`instance`定义了一个新的**类型类实例**。部分`Num a => Monoid (Product a)`有点类似于 Java 中的`class Product<T extends Number> implements Monoid<T>`——这意味着这里的`Product`类型变量被`Num`绑定，所以这个定义只涉及数字。

第二行和第三行定义了`Product`类型的`Monoid`函数实现。`mempty`只是一个包装在`Product`中的值 1，`mappend`函数在中缀符号中被定义为两个值相乘并再次包装在`Product`中。这个定义允许我们这样说:

`Sum`的`Monoid`类型类实例看起来非常相似。你可以看到**我们可以为同一个类型定义多个类型类实例，并且我们可以在已经定义了初始类型之后这样做**。

下面是我们如何定义一个函数，将多个幺半群值连接成一个，而不考虑它们的实际类型:

这类似于在 Java 中说`reduce(Monoid::append)`，但是，这里我们也可以指定空的幺半群值作为初始值，即使幺半群值的集合为空，也能得到合理的结果。

# 结论

如您所见，函数式编程中的类型类类似于面向对象编程语言中的接口，但同时它们代表了一个更强大的概念:

*   您可以为现有类型定义它们，而无需更改其签名；
*   您可以为同一类型定义多组实现。

希望这篇文章能激发您对 Haskell 编程语言的兴趣——让它成为您下一个要学习的编程语言！