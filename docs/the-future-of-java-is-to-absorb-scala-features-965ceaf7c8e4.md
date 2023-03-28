# Java 的未来是吸收 Scala 的特性

> 原文：<https://medium.com/codex/the-future-of-java-is-to-absorb-scala-features-965ceaf7c8e4?source=collection_archive---------3----------------------->

![](img/65716d87c4271a2eacdd25bc08ac8d43.png)

照片由[林赛·亨伍德](https://unsplash.com/@lindsayhenwood?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

距离 Java 17 的正式发布还有不到一周的时间，这是自 Java 11 以来的第一个 Java 长期支持(LTS)版本。2021 年 9 月 14 日，Java 17 运行时环境(JRE)和开发工具包(JDK)将提供下载和安装。

读完 Java 17 的特性，我更加确信 Java 的未来是慢慢地逐渐吸收 Scala 的特性。

众所周知，Scala 是 Java 虚拟机(JVM)的编程语言。Scala 编译器将 Scala 源代码编译成 JVM 字节码，就像 Java 编译器一样。

Scala 是由 Martin Odersky 创建的，他在 Java 1.3 编译器方面走在了时代的前面。他希望 Java 编程语言采用泛型。最后，在 Java 5 中增加了泛型，它使我们能够使用具有类型灵活性的数据结构，同时保持强大的类型检查(带有警告)。

例如，在 Scala 中，我们可能会写

```
var transactions: List[Transaction] = List()
```

而在 Java 中，我们可能会写

```
List<Transaction> transactions = new ArrayList<>();
```

在这两种情况下，如果您无意中试图将错误类型的元素添加到这样的列表中，编译器以及集成开发环境(IDE )(如果您使用 IDE)将会发出警告。

即使我们在运行时不能完全避免类型问题，编译时类型检查仍然是一个很大的帮助。

Scala 从一开始就有泛型。Odersky 希望从 Java 1.3 开始 Java 就有泛型，但那必须等到 Java 5。当然，Odersky 明白 Java 编程语言的发展受到向后兼容性的限制，现在仍然如此。

可以有把握地预测，Java 将会慢慢增加更多的 Scala 特性，因为它已经在增加 Scala 特性了。

让我们来看看一个相当小但却广为人知的特性:Java 10 中添加了`var`。让 Java 以冗长著称的一点是类型声明的明显冗余。举个例子，

```
SomeReallyLongClassName obj = new SomeReallyLongClassName(param);
```

像 IntelliJ IDEA 这样的集成开发环境(IDE)中的自动完成确实有所帮助，但是，必须重复长的类标识符往往会使 Java 程序员给实例取非常短的名字，比如`obj`和`param`，或者更糟，单个字母。

有了`var`，上面的例子变成了

```
var obj = new SomeReallyLongClassName(param);
```

在我看来，这没什么大不了的。更重要的是在 Java 8 中增加了 lambdas 和函数接口，这让 Java 更接近 Scala 的“函数是一等公民”的理念。

例如，假设在您的本地 Scala REPL 中，您有一个包含形式为 *n* + *n* + 1 的整数的`IndexedSeq[Int]`，例如 3、7、13、21、31、43、57、73、91 等。，为`res13`。

你想知道哪一个是质数，你有一个布尔函数，`isPrime()`，就是为了这个目的而定义的。

然后就是用`isPrime()`作为“谓词”(布尔函数的一个奇特的数学术语)进行过滤。实际上是自己写的:

```
scala> res13.filter(isPrime)
res14: IndexedSeq[Int] = Vector(3, 7, 13, 31, 43, 73, 157, 211, 241, 307, 421, 463, 601, 757, 1123, 1483, 1723, 2551, 2971, 3307, 3541, 3907, 4423, 4831, 5113, 5701, 6007, 6163, 6481, 8011, 8191, 9901)
```

如果你没有一个本地的 Scala REPL，你可以在一个 Scastie 片段中尝试一下，就像我[发布的](https://scastie.scala-lang.org/ffMCYaHjTZqjgGNyPaa68w)那样。

顺便说一下，Java 9 的 JDK 增加了 JShell，一个 Java 的本地 REPL。在没有 lambdas 或函数接口的情况下，您可以在 JShell 中获得相同的结果，但是会非常笨拙。因为这些是在 Java 8 中添加的，所以它们在 JShell 中也是可用的。大概是这样的:

```
res13.stream().filter(new Predicate<Integer> { @Override
    public boolean test(Integer v) {
        return isPrime(v);
    }});
```

有一种更好的方法，但对我来说它没有 Scala 的`filter(predicate)`语法那么明显(非常感谢 Julian，参见他 9 月 17 日的评论中的几个更好的选项)。

过滤的一个更普通的用途是将余额低于最小余额的账户列表放在一起。

```
val minBalAccounts = accounts.filter(_.balance < MINIMUM_BALANCE)
```

我把`_.balance`和`MINIMUM_BALANCE`想象成专门为金额设计的适当类型，而不是浮点数。我不认为 Java 会有操作符重载(除了`String`连接的特殊情况)，但是我可能错了。

无论如何，Java 程序员可能更需要大量其他 Scala 特性，比如 Switch-Case 语句的更复杂的模式匹配。

如果您查看我之前链接的 Scastie 片段，您会看到我是如何定义`isPrime()`函数的:

```
def isPrime(num: Int): Boolean = Math.abs(num) match {
  case 0 => false
  case 1 => false
  case n => (2 to 
      Math.floor(Math.sqrt(n)).toInt) forall (p => n % p != 0)
}
```

在 Java 中，你可以用一个 Switch-Case 来写，每个 Case 都有一个返回，这样你就不用担心 Switch-Case 因为忽略了 Breaks 而失败了。

您可以在 Scala 中编写更复杂的匹配用例语句，比如基于类型匹配的用例。举个例子，

```
urlConn match {
  case httpsConn: HttpsURLConnection => // TODO: Process HTTPS
  case httpConn: HttpURLConnection => // TODO: Process HTTP
}
```

你也可以把“警卫”放在箱子里。当然，你必须小心地从窄到宽安排你的案件。如果模式不太复杂，您的 IDE 可能会提醒您不可及的情况。

据宣布，Java 17 将为 Switch-Case 语句添加模式匹配，以及 Scala 中的 Case guards。

Java 17 也有望添加密封的类和接口，它们与 Scala 中的密封类和特征相对应。我将不得不另找时间详细说明这一点。

至于备受指责的分号，它们可能会留在 Java 中，因为添加分号推断可能比`var`之类的东西对向后兼容性的挑战更大。

我相信，只要我们继续使用电子计算机，Java 就会继续被使用。与此同时，专家们将继续预测 Java 即将灭亡。

也许 Martin Odersky 对 JVM 垃圾收集器的改进不太感兴趣(我知道我不感兴趣)，但是也许他关于 Java 编程语言应该如何发展的大多数想法都将会实现，在他第一次在 Scala 语言中提出这些想法之后很久。