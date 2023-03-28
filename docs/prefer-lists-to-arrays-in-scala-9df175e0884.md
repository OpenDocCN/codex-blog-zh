# Scala 中更喜欢列表而不是数组

> 原文：<https://medium.com/codex/prefer-lists-to-arrays-in-scala-9df175e0884?source=collection_archive---------4----------------------->

## [法典](http://medium.com/codex)

![](img/54f1c1c7cebc14572b77c4a2f5715e66.png)

[freestocks](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Java 提供了数组和列表。Scala 也是如此。Java 列表比数组有很多优势。在 Scala 中，列表比数组的优势甚至比 Java 更大。

因为大多数学习 Scala 的人都是从 Java 的角度来学习的，所以我们将从回顾 Java 中声明和初始化数组的几种方式开始。

这里有一种在 Java 中声明由五个特定整数组成的数组的方法:

```
int[] numbers = {43, -7, 8, 21, 58};
```

还有一些其他的方法，但是它们都涉及到“方括号”和“花括号”

```
int[] numbers = new int[5];
numbers[0] = 43;
numbers[1] = -7; // etc.
```

Scala 的语法有所不同，但是我想你会很快习惯的。

```
val numbers = Array(43, -7, 8, 21, 58)
```

注意，那些是圆括号，不是“方括号”也不是“花括号”更有可能的是，在回到 Java 的过程中，你会忘记 Java 数组语法，而不得不在网上查找。我知道我有。

在本地 Scala REPL 上，你可以得到关于类型推断的即时反馈。

```
scala> Array(43, -7, 8, 21, 58)
res2: Array[Int] = Array(43, -7, 8, 21, 58)
```

上面显示的第一行 Java 代码和带有“`val`”的 Scala 代码都编译成了几乎相同的东西。如果您使用 Java 反汇编工具(`javap`和`-c`命令行选项)，您会看到很多不同之处，但是您也会看到很多连续的行是完全相同的。

```
 0: iconst_5            // Push 5 (array size) on the stack
       **1: newarray      int**   // New int array
       3: dup                 // Duplicate top stack element
       4: iconst_0            // Push 0 (first index) on the stack
       **5: bipush        43**    // Push 43 (1st element) on the stack
       7: iastore             // Store 43 at array index 0
       8: dup
       9: iconst_1            // Push 1 (second index) on the stack
      **10: bipush        -7**    // Push -7 (2nd element) on the stack
      12: iastore             // Store -7 at array index 1
      13: dup
      14: iconst_2            // Push 2 (third index) on the stack
      // etc.
```

就 JVM 而言，Scala 的`Array[Int]`和 Java 的`int[]`完全一样。

注意，在 Scala 中,“方括号”`[`和`]`不像在 Java 中那样用于数组。相反，它们的作用与 Java 中的“尖括号”`<`和`>`相同:为像`ArrayList<E>`这样的泛型类型括起类型参数。

本质上，您应该将 Scala 中的数组视为非常类似于 Java 开发工具包(JDK)中的泛型类型、Scala 开发工具包(SDK)中的泛型类型、第三方库中的泛型类型以及您可能自己设计的泛型类型。

这意味着，例如，如果你想在你的 Scala 程序中使用一个 Java 数组列表，你应该像这样声明它:

```
 val reindeerNames = new util.ArrayList**[**String**]**()
    reindeerNames add "Dasher"
    reindeerNames add "Dancer"
    reindeerNames add "Prancer" // etc.
```

尽管在我看来，在 Scala 项目中使用任何`java.util`数据结构的唯一理由是为了 Java 的互操作性(例如，框架中的函数需要一个`ArrayList<E>`)。

该表扬的地方一定要表扬:当事先不知道元素的确切数量时，Java 数组列表提供了比直接使用数组更重要的好处，比如自动调整后备数组的大小。

尽管如此，最好在 Scala 程序中使用 Scala 集合。

```
val reindeerNames = List("Dasher", "Prancer", "Vixen", "Comet",
                         "Cupid", "Donner", "Blitzen", "Rudolph")
```

如果相关类型`T`是`Comparable<T>`(Scala 特性`Ordered[T]`扩展了 Java 的`Comparable[T]`)，那么对数组排序就很容易了。

```
scala> numbers.sorted
res3: Array[Int] = Array(-7, 8, 21, 43, 58)
```

不要指望用 Java 就能做到。在 Scala 中可以这样做的唯一原因是隐式转换为`ArrayOps[T]`。

但是要注意:数组是可变的数据结构，伴随而来的是所有的问题，包括并发性问题。

```
scala> numbers(0) = 74scala> numbers
res5: Array[Int] = Array(74, -7, 8, 21, 58)
```

在像`numbers`数组这样的玩具例子中，可变性根本不是问题。但是，在一个程序中，即使只有两个并发线程试图访问同一个数组，也可能会遇到瓶颈和竞争等问题。

你们中的一些人可能对编写多线程程序不感兴趣。尽管如此，列表仍然比数组更好，因为心理模型:作为人类，我们倾向于考虑列表而不是数组。

例如，当你去购物时，你经常会有一个购物清单，但你可能从来没有过“购物清单”你的购物并不受限于任意数量的购物空间，而是受限于你能在这些商品上花多少钱，以及这些商品的价格。

也许你想购买某个品牌的 10 个小配件，但你发现了一个交易，可以买两个小配件，送一个。所以，也许你接受了这笔交易，经常以这个价格购买 15 个小配件。

因此，如果你用 Java 或 Scala 在网上购物车中实现购物车，你可能应该使用来自 JDK 的`java.util`包或 SDK(来自`scala.collection.immutable`或`scala.collection.immutable`)的集合，而不是数组。

在某些情况下，比如在与 Java 框架交互操作时，您需要使用数组。但是由于`ArrayOps`中的`toList()`和`toSet()`函数，它们可以方便地转换成列表或集合。

```
scala> numbers.toList
res6: List[Int] = List(43, -7, 8, 21, 58)scala> numbers.toSet
res7: scala.collection.immutable.Set[Int] = HashSet(-7, 21, 43, 8, 58)
```

这对于元素很少的玩具例子来说很好。当你需要一次处理成千上万个对象的时候呢？例如，假设您想要前一百万个素数的列表。

一个简单的 Java 实现使用一个百万整数原语的数组，并且只进行最基本的优化(跳过所有大于 2 的偶数，只检查最大到 **√** *n* 的潜在因子)，可以在不到三秒的时间内交付结果。

与使用不可变列表的 Scala 实现相比:这将花费很长时间，你可能会在几分钟后停止它，如果不是更早的话。只是在用`scala.collection.mutable.ArrayBuffer`替换了`List`之后，我才能够将运行时间降低到 4 秒以下。

但是…这个有一百万个素数的例子不太现实。你有多经常需要从几百万个整数中筛选出一百万个呢？可能不经常。

总的来说，Scala 的数组主要用于与 Java 的互操作。在大多数情况下，更快的数组访问速度对于您不愿意使用 C 或 C++之类的东西来说几乎没有任何优势。