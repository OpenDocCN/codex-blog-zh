# 面向 Java 开发人员的 Kotlin 简介—第 1 部分:函数和运算符

> 原文：<https://medium.com/codex/things-to-love-in-kotlin-coming-from-java-8-part-1-functions-and-operators-40aea17c8854?source=collection_archive---------13----------------------->

![](img/107a6ff9dc060566d1616bd0ef461647.png)

在本系列的第一部分中，我将带您参观 Kotlin 中的函数和操作符。我将概述它们在哪些方面与 Java 不同。我希望你喜欢读这篇文章，就像我喜欢写它一样！如果你做到了，别忘了鼓掌:-)

本系列的其他部分:

*   面向 Java 开发人员的 Kotlin 简介—第 2 部分:类和对象
*   [面向 Java 开发人员的 Kotlin 介绍—第 3 部分:泛型](/codex/introduction-to-kotlin-for-java-developers-part-3-generics-21e1646ec2ae)

Java 创建于 26 年前的 1995 年。第 8 版于 2014 年发布，至今仍是使用最广泛的版本。它主要介绍了 lambda 表达式、流 API 和一个新的日期/时间 API。在我看来，从那时起，就没有推出真正的杀手级功能。迁移到新版本的痛苦大于好处，大多数人坚持了下来。

Kotlin 是 10 年前由 Jetbrains 在 2011 年创建的。像 Java 一样，它编译成在 JVM 上运行的字节码(尽管它也可以转换成 Javascript 或编译成本机代码)。它被设计成 100%与 Java 互操作，这意味着可以从 Kotlin 调用 Java 代码，反之亦然。自 2017 年以来，它被谷歌官方支持为 Android 语言，80%的顶级应用程序都在使用它。

# 1.功能

Kotlin 函数可以在文件的顶层声明，这意味着您不需要创建一个类来保存函数，而在 Java 中您需要这样做。

用 fun 关键字声明的函数可以是命名的，也可以是匿名的。它们必须声明它们的返回类型(为了可读性，并且因为它们的主体可以包含复杂的控制流，使得编译器很难或者不可能推断出返回类型)。它们必须使用 return 关键字返回值(与 lambda 表达式相反——稍后将详细介绍)。

示例:

与 Java 函数相比，Kotlin 函数有许多与众不同的特性。请继续阅读，寻找答案。

[科特林文档](https://kotlinlang.org/docs/functions.html)

## 1.1 默认参数

函数参数可能有默认值，因此与 Java 相比减少了重载次数:

[科特林文档](https://kotlinlang.org/docs/functions.html#default-arguments)

## 1.2 命名参数

当调用一个函数时，可以命名它的一个或多个参数。当函数有大量参数时，这可能很有帮助。它也可以与构造函数一起使用，因此在许多情况下，定义繁琐的构造函数来维护构造函数变得没有必要。

[科特林文档](https://kotlinlang.org/docs/functions.html#named-arguments)

## 1.3 接收器的功能

带有接收方的函数是可以在类型 A 的对象上调用的函数，就像类型 A 的常规成员函数一样，尽管它不是类型 A 的一部分。

**1.3.1 扩展功能**

Kotlin 中的扩展函数是带有接收器的函数，它提供了在现有类上移植新功能的能力，而不用以任何方式修改它。

扩展函数类似于常规函数，只是被扩展的类型(称为接收器类型)必须作为前缀添加到函数名中，后面跟一个点。在扩展函数的主体中，调用该函数的对象是可用的`this`。

下面是一个向类型`MutableList<Int>`添加交换成员函数的例子:

扩展函数对于向第三方库的类中添加新成员很有用。这实际上是 Kotlin 本身如何将自己的附加特性添加到常规 JDK 集合类型中的。下面是 Kotlin 在 ArrayList 上定义的扩展函数的[列表。](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-array-list/#extension-functions)

[科特林文档](https://kotlinlang.org/docs/extensions.html)

**1.3.2 带接收方的函数字面量**

Lambdas 和匿名函数也可以用来定义带有接收方的函数。稍后会详细介绍。

[科特林文档](https://kotlinlang.org/docs/lambdas.html#function-literals-with-receiver)

## 1.4 中缀函数

带有单个参数的扩展函数或成员函数可以用关键字`infix`标记，这样就可以在函数调用中去掉点号和括号。

下面是一个例子:从两个值创建一对的[到](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/to.html)扩展函数。

例如，它可用于初始化地图，如下所示:

[科特林文档](https://kotlinlang.org/docs/functions.html#infix-notation)

# 2.λ表达式

Lambda 表达式是只用花括号声明的函数(没有有趣的关键字)。它们不能使用 return 关键字，它们只是对最后一个表达式求值，因此编译器总是可以推断出返回类型(假设参数类型是声明的或者可以从上下文中推断出来)。事实上，没有声明返回类型的语法。

示例:

[科特林文档](https://kotlinlang.org/docs/lambdas.html)

## 2.1 单参数λ

如果一个 lambda 只有一个参数，允许不声明它并省略`->`。该参数将以名称`it`隐式声明。

[科特林文档](https://kotlinlang.org/docs/lambdas.html#it-implicit-name-of-a-single-parameter)

## 2.2 拖尾 lambdas

在 Kotlin 中，如果函数的最后一个参数是函数，那么作为相应参数传递的 lambda 表达式可以放在括号外。此外，如果 lambda 是唯一的参数，括号可以完全省略。

下面是一个使用 [let](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/let.html) 扩展函数的例子，它可以用来调用一个或多个函数来处理调用链的结果，避免了声明变量的需要:

这种写作风格有以下优点:

*   它避免了在离调用点很远的地方用括号结束函数调用
*   嵌套的 lambdas 可以像嵌套的代码块一样格式化
*   库可以像 Java 的 try 一样用资源定义语言结构作为常规函数

这一点，再加上操作符重载和带有接收器的函数，使得 Kotlin 在创建类型安全 DSL 时非常优雅，就像这个 [HTML DSL](https://kotlinlang.org/docs/typesafe-html-dsl.html) :

[科特林文档](https://kotlinlang.org/docs/lambdas.html#passing-trailing-lambdas)

## 2.3 带接收器的 Lambdas

匿名函数和 lambda 表达式可以用来定义带有接收方的函数，就像常规函数一样，类似地，在这些函数的主体中，调用函数的对象变成了隐式的`this`。

下面是一个带有功能的[的例子:](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/with.html)

使用`with`将`myTurtle`设置为下一个代码块中的`this`，从而允许在同一个对象上调用多个函数，而无需重复变量名。

[科特林文档](https://kotlinlang.org/docs/lambdas.html#function-literals-with-receiver)

# 3.关闭

闭包是一个函数、匿名函数或 lambda，它引用在其主体外部定义的变量。

这样的事情是可能的，因为闭包保存了对创建它的堆栈框架的引用。并且该堆栈框架属于一个函数，该函数本身保存了对它被创建到的堆栈框架的引用，以此类推，创建了一个称为“作用域链”的堆栈框架链(只有当不再有闭包引用它时，堆栈框架才会被释放)。

这允许有趣的模式，比如[用私有成员和函数模拟没有内置该特性的语言中的类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures#emulating_private_methods_with_closures)。

Java 8 有闭包……算是吧。Java 的主要限制是在闭包中访问的外部作用域的变量必须是 final 或者实际上是 final。在 Kotlin 中，这个限制被取消了，闭包可以通过作用域链改变任何可用的变量。

这里有一个例子:

还有一个:

[科特林文档](https://kotlinlang.org/docs/lambdas.html#closures)

# 4.内嵌函数

在 Kotlin 中，函数可以标记为 inline。这类函数的行为类似于 C 宏，函数体是内联的，任何作为参数传递的 lambda 也是内联的。这消除了函数调用的成本和分配 lambda 对象及其相应闭包的成本。

例如， [forEach](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/for-each.html) 是一个内联函数。

当函数及其 lambda 参数被内联时，lambda 表达式中允许 return，并且 return 是非局部的。对 return 的调用涉及最近的封闭函数(即用 fun 定义的函数)。例如:

暂时不支持“中断并继续”,但将来会支持。

[科特林文档](https://kotlinlang.org/docs/inline-functions.html)

# 5.运算符重载

Kotlin 允许您为 t 类型上的一组预定义操作符提供自定义实现。这些操作符中的每一个都对应于一个您必须实现的特殊函数名(作为成员函数或作为扩展函数),并在 t 类型上用`operator`关键字进行标记。操作符可以是一元的，也可以是二元的。

[科特林文档](https://kotlinlang.org/docs/operator-overloading.html)

## 5.1 一元运算符

一元运算符例子有`-a`(函数名`unaryMinus`)、`a++`(函数名`inc`)和`a()`(函数名`invoke`)。

示例:

[科特林文档](https://kotlinlang.org/docs/operator-overloading.html#unary-operations)

## 5.2 二元运算符

二元运算符的例子有`a + b`(函数名`plus`)、`a in b`(函数名`contains`)、`a[i]`(函数名`get`)。

示例:

[科特林文档](https://kotlinlang.org/docs/operator-overloading.html#binary-operations)

# 资源

[科特林文档](https://kotlinlang.org/docs/home.html)

[科特林游乐场](https://play.kotlinlang.org/)

科特林·考恩斯