# Scala 中的密封类和特征

> 原文：<https://medium.com/codex/sealed-classes-and-traits-in-scala-62e6076053c6?source=collection_archive---------3----------------------->

![](img/e50c8636e2c6bb1da0eb3ca441a26cba.png)

照片由[艾米·阿什](https://unsplash.com/@amyannaasher?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

昨天，9 月 14 日，是 Java 17 的正式发布日，这是几年来第一个长期支持(LTS)版本(之前的 LTS 版本是三年前发布的 Java 11)。

Java 17 的新特性之一是增加了密封类和接口。这对那些只使用 Java 编程的人来说很陌生，但对那些也使用 Scala 编程的人来说并不奇怪。

[我上周写了](/codex/the-future-of-java-is-to-absorb-scala-features-965ceaf7c8e4)Java 的未来是吸收 Scala 特性。当然，不是每一个特性，只是那些被认为是有益的，并且可以在不破坏向后兼容性的情况下引入 Java 的特性。

Scala 中的密封类和密封特征被认为是 Java 值得拥有的。在“`class`”或“`interface`”之前添加`sealed`修饰符不会破坏现有的程序，因为如果它们是早期 Java 版本的有效程序，它们在上下文中就不会有这个词。

仅仅因为 Scala 程序员对 Java 的这个新特性并不感到惊讶，并不一定意味着他们知道它是如何工作的，或者他们已经在自己的 Scala 程序中使用了这个特性。

在本文中，我将概述 Scala 中密封类和特征的基础知识。我还会给出几个具体的例子，希望能给你一些关于如何在自己的 Scala 程序中使用密封类和特征的想法。

在 Java 和 Scala 中，一个类都可以被标记为 final(当然 Java 接口成为 final 是没有意义的)。如果它不是最终的，一个非私有类可以被同一个包中的任何其他类扩展，或者被模块中的任何类扩展，或者如果它是公共的，被“世界”扩展。

但是，在某些情况下，您可能希望允许一个类被扩展，但是只在某些非常特殊的条件下。

对于 Match-Case 语句中的模式匹配，访问修饰符，即使 Scala 能够比 Java 更精确地指定访问范围，也可能不如您所希望的那样细粒度。

例如，假设您想要根据继承的下一个较低级别的子类匹配一个`java.net.URLConnection`对象(不考虑运行时类)。

该对象可能是一个`HttpURLConnection`或一个`JarURLConnection`。这些可能是 Java 开发工具包中唯一的“主要”类型，但不能保证它们是特定系统配置中的唯一类型。

所以你必须为一个意外的主要类型提供一个默认的案例，比如`FtpURLConnection`，或者容忍一个非穷举的匹配警告，以及运行时可能出现的`MatchError`。

用 Scala 创建的类层次结构也是如此。但是在 Scala 中，你总是有第三种选择:密封类。

现在，在 Java 17(或更早的 Java 15，如果您启用了预览功能)，您也可以在 Java 中使用该选项。

密封类意味着它只能由您指定的类(或特征，而不是接口)来扩展。试图用未指定的类扩展密封类会导致编译错误。

在 Scala 中，您可以通过将子类或子目录放在同一个源文件中来指定，稍后您将在示例中看到。

在 Java 中，看起来你必须写一个许可条款(例如，“`sealed class Shape permits Circle, Triangle, Square,`”等等)。)，我猜它们一定在单独的源文件中。

我不认为`Shape`类是密封类的好用例。也许棋子是一个更好的用例，因为棋子的数量比二维图形的数量要少得多。

如你所知，国际象棋是两个人的游戏。每个玩家得到十六个棋子:一个国王，一个王后，两个象，两个骑士和两个车，以及八个棋子。不同的部分可以由具有公共超类的不同类的实例来表示。

如果我们密封了那个公共超类，那么编译器知道这些是那个超类存在的唯一子类。

有了密封的`Piece`类，编译器现在知道一个`Piece`实例只能是一个`King`、`Queen`、`Bishop`、`Knight`、`Rook`或`Pawn`。如果我们想要更多的片段，比如大主教和大法官，我们只需要将它们添加到源文件中。

注意`Pawn`是抽象的。但也许所有这些都需要是抽象的。请随意使用这个例子。

您可能会觉得 Scala 中的密封类就像 Java 中的枚举类型(或“枚举”)。有时它们被用于这个目的。例如，我们可以，

```
sealed class DayOfTheWeekobject SUNDAYobject MONDAYobject TUESDAY// etc.
```

记住，就 Java 虚拟机(JVM)而言，没有枚举类型或“枚举”这样的东西，只有扩展了`Enum<E>`的最终类`E`。

这给了我尝试编写一个扩展`java.lang.Enum[E]`的密封 Scala 类的想法。这是一个充满问题的兔子洞。密封的类和特征并不是 Java 枚举类型的答案。

据我所知，密封抽象类和密封特征之间的区别与非密封抽象类和非密封特征之间的区别是一样的。

首先，这意味着一个类只能扩展一个密封类的一个子类，但是一个类可以实现一个密封特征的多个子树。

考虑这个例子:

那么我们可能会有这样的情况:

```
class Knight extends Piece **with CanJumpOver with SomeOtherTrait** { override val notation: String = "N" **override val minimumMoveDistance: Int = 3** **override val maximumMoveDistance: Int = 3**}
```

我这样设置，`Knight`可以直接扩展`Piece`，但不能直接实现`Movable`。也许这是这个特定用例的正确设计。

我不知道是否有办法在运行时绕过密封，但我知道本地的 Scala REPL 不允许这样做。

```
scala> class Archbishop extends chess.pieces.**Piece** with chess.pieces.CanJumpOver
                                             **^**
       **error: illegal inheritance from sealed class Piece**scala> class Chancellor extends chess.pieces.**Movable**
                                             **^
       error: illegal inheritance from sealed trait Movable**
```

我还没有看到 Scala 编译器如何编译密封的类和特征，也没有看到它们如何出现在 JVM 中。很可能已经超越了基础。

简而言之，Scala 中密封类和特征的基本思想是，它们使您能够在一个层次上限制继承层次，而不限制它在直接子类或子类型之下的任何层次上。

这就是 Scala 中密封类和特征的基础。我希望这篇介绍能给你一些如何在自己的 Scala 项目中使用它们的想法。