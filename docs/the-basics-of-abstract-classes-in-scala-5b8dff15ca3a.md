# Scala 中抽象类的基础

> 原文：<https://medium.com/codex/the-basics-of-abstract-classes-in-scala-5b8dff15ca3a?source=collection_archive---------10----------------------->

![](img/9e371185e045bc28a7047e12988177da.png)

这种戏剧性的棋子排列在真实的国际象棋比赛中永远不会发生。费利克斯·米特迈尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果您了解 Java 中的抽象类，并且对 Scala 语法有所了解，您可能会认为您已经知道了抽象类在 Scala 中是如何工作的:与 Java 中的非常相似，只有一些小的不同。

你是对的。Scala 抽象类很像 Java 抽象类。不过，微小的差异可能偶尔会让你犯错。或者它们可能是令人愉快的惊喜。

对于本文，我将假设您确实了解 Java 中的抽象类，所以我不会重复 Java 语法，除非与 Scala 语法进行比较和对比。

我还假设你首先已经知道了使用抽象类的原因，所以我也不打算赘述。

例如，我可以使用银行账户:`Account`是一个抽象类，`CheckingAccount`和`SavingsAccount`是`Account`的“具体”子类。

我认为这是一个很好的例子。但是我更喜欢不那么世俗的东西，那么，象棋怎么样？作为一个例子，象棋的唯一缺点是，不是每个阅读这篇文章的人都知道如何玩。我不打算在国际象棋上做太深入的探讨，我会尝试解释所有可能不明显的事情。

你有一个 8 乘 8 的棋盘，两边各有 16 个棋子。游戏的目标是困住对手的国王，同时保证自己的国王安全。你所有的其他棋子都是可牺牲的，但是比你的对手有更多的棋子通常会有所帮助。

每个棋子都有自己的起始位置和在棋盘上移动的方式。但是不同的部分肯定有足够的共同点，为它们创建一个抽象超类可能是值得的。

```
package chessabstract class ChessPiece
```

在第一行末尾添加一个分号，在最后一行末尾添加一对花括号，这样 Java 编译器就不会有问题了。

但是请注意，在 Java 上下文中，如果`ChessPiece`没有被显式标记为 public，那么它在`chess`包之外是不可访问的。由于大多数 Java 类最终都被声明为 public，Martin Odersky 认为 public 作为 Scala 类和特征(接口)的默认设置更有意义。

与 Java 一样，Scala 中的抽象类可能有抽象的“方法”。

```
package chessabstract class ChessPiece { abstract def move(position: Position)}
```

`move()`的抽象修饰语是多余的，可能会被省略，就像备受争议的分号也可以被省略一样(尽管加入分号会给出警告)。

```
package chessabstract class ChessPiece { def move(position: Position)}
```

`ChessPiece`的子类，比如`Pawn`和`Rook`(看起来像塔的那个)就要定义`move()`，除非他们本身也是抽象类。

无论是否抽象，Java 子类都不能覆盖超类的字段。当子类有一个与超类字段同名(但不一定是相同类型)的字段时，超类字段是“隐藏的”，但仍然可以访问。子类字段实际上并不覆盖超类字段。

字段隐藏迷惑了许多 Java 初学者，甚至一些专业人员。当 Martin Odersky 设计 Scala 时，他显然认为这是他不想在 Scala 中使用的 Java 特性之一。

所以在 Scala 中，子类字段可以覆盖超类字段，语法非常类似于覆盖超类函数和过程的语法。

对于下一个例子，我将使用一个棋子的布尔属性`canJumpOver`。大多数棋子不能跳过任何一方的其他棋子，但骑士可以(骑士是看起来像马的棋子)。

在游戏开始时，任一玩家可以选择让他们的一名骑士跳过己方的棋子。在游戏的后期，跳过任何一方的棋子的能力都会派上用场。

其他棋子都不能跳过其他棋子。例如，皇后能够一步就从棋盘的一边移动到另一边。但在游戏开始时，女王被前面的棋子和两边的主教和国王包围。

因此，`ChessPiece`声明`canJumpOver`为假是有意义的。

```
package chessabstract class ChessPiece {
  val canJumpOver: Boolean = false def move(position: Position)}
```

然后`Knight`可以覆盖它，就像它覆盖`move()`一样。

```
package chessclass Knight extends ChessPiece {
  **override** val canJumpOver: Boolean = **true** **override** def move(position: Position): Unit = {
    // TODO: Figure out what this needs to do
  }}
```

或者，我们可以这样做:

```
package chessabstract class ChessPiece {
  val canJumpOver: Boolean def move(position: Position)}
```

但是那样我们就不得不在`Queen`、`Bishop`、`Knight`等等中写下`override val canJumpOver: Boolean = true`。那就不会很“干”(意思是“不要重复自己”)。

除非这些类也是抽象类。尽管把这个特殊的罐子踢到后面真的没有多大意义。

如果我们用 Java 来做这件事，我们可能应该使它成为一个布尔调用，而不是一个布尔字段，以避免字段隐藏带来的问题，例如:

```
package chess;public abstract class ChessPiece {

    **public boolean canJumpOver() { 
        return true;
    }** abstract void move(Position position);}
```

也许 Java 和 Scala 版本的`ChessPiece`编译成相同的东西，或者几乎相同的东西。但是希望 Scala 版本比 Java 版本更直观。

就是这样，这些就是 Scala 中抽象类的基础。没什么大不了的，这是一个基于 Java 概念的简单概念。