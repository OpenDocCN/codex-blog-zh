# 当 Java 不支持缺省函数参数时，Kotlin 是如何支持的

> 原文：<https://medium.com/codex/how-kotlin-supports-default-function-parameters-when-java-does-not-cd3b839b8089?source=collection_archive---------6----------------------->

您可能已经使用了 Kotlin 的默认函数参数来简化函数调用和封装默认行为。但是，Java 不提供这种支持。然而，两种语言都是可互操作的，当目标是 Kotlin/JVM 时，Kotlin 代码甚至被编译成 Java 字节码。本文帮助您理解 Kotlin 如何在支持默认参数的同时保持与 Java 的互操作性。我们走吧！

![](img/2925eb7cbbd368da6d19712cb1e565b1.png)

图片由[蒂姆希尔](https://pixabay.com/photos/ribblehead-viaduct-viaduct-bridge-2443085/)

# 互操作性的含义是什么？

在本文中，我们讨论了 Kotlin 与 Java 的互操作性，也就是说，当 Kotlin 被编译成目标 Kotlin/JVM 时。简而言之，

> Kotlin 和 Java 文件可以存在于同一个项目中，可以直接相互通信。你可以从 Kotlin 调用 Java 方法，从 Java 调用 Kotlin 函数。同样，你可以在 Kotlin 中使用 Java 的 API 和框架，反之亦然。

# 默认参数(缺乏)互操作性

让我们来看看当我们用默认参数编译一个 Kotlin 函数时，在幕后发生了什么。

将字节码反编译成 Java，我们得到了不支持任何缺省参数的`sample`的这个定义(为了清楚起见进行了简化):

注意，Kotlin 版本的`sample`有四种不同的调用方式:

然而，只有显式传递了所有两个参数，才能调用更详细的 Java 版本。也就是说，当从 Java 文件中调用 Kotlin 函数时，我们会得到:

# 默认参数(更好的)互操作性

为了在更具体的情况下提供与 Java 更好的互操作性，JVM 提供了几个注释来指导编译器如何编译 Kotlin 代码。例如，`@JvmOverloads`的注解

> *指示 Kotlin 编译器为给定函数生成替换默认参数值的重载。*

实际上，这意味着通过向 Kotlin 函数添加`@JvmOverloads`注释，您可以要求 Kotlin 编译器通过以不同方式重载该方法来尝试重现默认的参数行为。例如:

现在反编译成下面的方法重载。为了简单起见，我们抽象了使用合成方法的 Java 实现逻辑。

实际上，这意味着我们现在可以从 Java 调用，而不会出错:

# 限制

很好，我们了解到通过使用方法重载，Java 能够与 Kotlin 的默认参数函数提供更好的互操作性。但是你可能已经注意到在上面的例子中仍然有一个缺失的案例。如果我们想用默认的`a`参数和自定义的`b`来调用`sample`呢？

这在 Kotlin 中是可能的，因为它支持[命名参数](https://kotlinlang.org/docs/functions.html#named-arguments)，但在 Java 中不支持。由于参数`a`和`b`都是类型`String`，方法重载不能区分它们，所以我们不能用 Java 复制上面的调用。

感谢阅读！如果你喜欢的内容，不要忘记鼓掌，并关注更多！