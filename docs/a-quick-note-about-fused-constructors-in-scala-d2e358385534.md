# 关于 Scala 中融合构造函数的快速说明

> 原文：<https://medium.com/codex/a-quick-note-about-fused-constructors-in-scala-d2e358385534?source=collection_archive---------5----------------------->

![](img/4068eb30b9fe56ba963d71a173599b8c.png)

Benjamin Wedemeyer 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 Scala 中，感觉我们在类中编写的构造函数比在 Java 中要少。这在技术上可能是正确的，这要感谢融合构造函数，它在某些情况下是自动提供给我们的。

我们可以依赖 Java 编译器自动提供给我们的唯一构造函数是零参数构造函数，它不做任何字段初始化，当然假设我们不提供任何显式构造函数。否则，我们必须在一个 Java 类中显式地编写所有的构造函数。

如果我们需要构造函数参数，以及 Java 中的参数验证和字段初始化，我们必须把它们都写出来。

这里有一个典型的例子:

```
public class Point { private final int pX, pY; public Point(int x, int y) {
        this.pX = x;
        this.pY = y;
    }}
```

注意，这应该是一个不可变的类，不像`java.awt`包中的`Point`。

在 Scala 中，我们可以将不可变的`Point`缩写为

```
class Point(val x: Int, val y: Int) {}
```

如果没有花括号括起来的内容，我们也可以完全省略它们。即使我们被期望为`x`和`y`提供 getters，我们也可以省略花括号。

这两个例子应该编译成本质上相同的东西。在 Scala 示例中，主构造函数(唯一的构造函数)与类声明融合在一起。

但是，如果我们需要这个融合的主构造函数做其他事情，比如参数验证，该怎么办呢？我们可以把它放在花括号中，只要它不在`def`中。`val`和`var`都将为此工作。

例如，在我的铜山推箱子项目中，我有一个抽象的`PieceGroup`类，它有三个参数，其中一两个可能为空。也许对我的用例来说有更好的方法，但是对这里的例子来说这样就很好了。

以下是来自我的 GitHub 库的相关摘录[:](https://github.com/Alonso-del-Arte/copper-mountain-sokoban/blob/main/src/sokoban/pieces/groupings/PieceGroup.scala)

```
abstract class PieceGroup(val pieceA: Piece,
                          val pieceB: Piece,
                          val pieceC: Piece = null)
```

只要我们小心确保它只发生在我们期望它发生的地方，Null 就没有什么可怕的。所以`pieceC`可能为空，但是如果`pieceB`不为空，那么`pieceA`也一定不能为空。

我想我可以让`PieceGroup`成为一个密封的类，这样我就可以确定所有的子类构造函数都以正确的顺序发送参数。但是我也觉得我需要一个`size()`函数来查询一个棋子组有多少个棋子。然后我想到:把那个函数融合到融合的构造函数中，就像这样:

```
abstract class PieceGroup(val pieceA: Piece,
                          val pieceB: Piece,
                          val pieceC: Piece = null) { **val size: Int** = (this.pieceA, this.pieceB, this.pieceC) match {
    case (null, null, null) => 0
    case (null, _, null) =>
      val excMsg = "Non-null piece should be first"
      throw new NullPointerException(excMsg)
    case (_, null, null) => 1
    case (_, _, null) => 2
    case (null, null, _) => val excMsg = "Non-null should be first"
      throw new NullPointerException(excMsg)
    case (null, _, _) => val excMsg = "Null piece should be last"
      throw new NullPointerException(excMsg)
    case (_, null, _) => val excMsg = "Null piece should be last"
      throw new NullPointerException(excMsg)
    case _ => 3
  }

}
```

如果有放错位置的空值，它们将导致`NullPointerException`发生，并阻止子类构造函数完成。

这使得我的空放置测试从失败到通过，以及对`size()`的测试——从 Java 的角度来看，这是一个返回类型`int`的“方法”,以及一个由构造函数设置的字段的字段获取器，就 Java 虚拟机而言。

为了这篇文章，我使用 javap 工具来检查这些东西是如何编译的，我所看到的或多或少是我所期望的。但是因为我有单元测试，所以有一种更简单的方法来消除我对这个概念的理解中的任何疑虑。

我将“T5”改为“T6”，然后运行测试。`size()`测试通过，但空位置测试失败。用"`def`"代替"`val`，"`size()`是一个必须在已经构造的实例上显式调用的函数。恢复"`val`"将它恢复到一个在构造时运行的字段初始化器。

Scala 中的融合构造函数概念来自 Java，需要一些时间来适应。在很大程度上，我喜欢它。

我不太喜欢这些融合构造函数的唯一一点是，它们不能像 Java 构造函数那样有严格的局部值。在我看来，减少“样板文件”不仅仅是一个值得的交易。