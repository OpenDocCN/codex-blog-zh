# flog:Scala 的一个函数式日志记录器

> 原文：<https://medium.com/codex/flog-a-functional-logger-for-scala-986d4039562f?source=collection_archive---------4----------------------->

不幸的是，记录可能是一件好事，也可能是一件坏事。对于生产系统，日志记录是一门独立的学科。但是，在代码开发中，它似乎并没有得到足够的使用。它应该是如此简单，以至于当试图理解为什么代码不能像预期的那样工作时，它是第一个，而不是最后一个手段。这一点在函数式编程语言中尤为明显。那是因为测井是以*语句*为导向，而不是以*表达式*为导向。即使您不想麻烦日志记录，而是使用 *println* ，您仍将不得不与您的代码搏斗，因为 *println* 产生*单元*，即它是面向语句的。

![](img/216c16ae2f70e56570004e88eaa62e68.png)

这就是为什么我开发了 **Flog** ，一个 Scala (2)的函数式日志记录器。真的很好用。假设你想定义一个类型为 *X* 的 **val** *x* 作为某个表达式( *expr* )，你写:

```
val x: X = expr
```

但是现在您决定想要知道 *expr* 的实际值是多少。只需添加一个*消息*(日志消息，一个*字符串*)和操作符！！如下所示:

```
val flog = Flog[MyClass]
import flog._// other codeval x: X = msg !! expr
```

就是这样！基本上你要做的就是这些。一个 *expr* 的表示将被附加到日志中，否则，程序的行为将与之前完全一样。但是，如果您不想在日志中再次看到该值，该怎么办呢？只需更换！！运算符 by |！并且程序将同样运行，但是此时没有日志记录(哦，顺便说一下， *msg* 不会被求值)。

的！！如果使用标准记录器，操作员对应于*信息*。也可以用！？对于*调试*或者！？？对于*跟踪*。

或者，你可以通过简单地将 *Flog()* 替换为 *Flog* ()，来去除 *Flog* 在模块中的所有用法。*禁用*。最终，当您确定再也不想记录这个表达式时，您只需删除 *msg |！*共构。

默认情况下， *Flog* 使用的实际日志记录实用程序是*org . slf4j . logger factory . get logger*用于 *Flog* 类本身。对于严肃的使用，你会想要为你自己的类使用一个记录器。您可以通过将 *flog* 实例化为:

```
val flog: Flog = Flog[MyClass]
```

但是，您可能也想知道记录器如何知道如何表示类型 *X* 的实例。如果 *X* 是某种复合但熟悉的类型，如 *Iterable[Int]、Future[Double]、*或 *Option[String]* (还有许多其他受支持的容器)，那么记录器将只做合理的事情，而不需要您做任何额外的工作。但是如果 *X* 是自己代码中定义的类型呢？是**案例类**(还是其他*产品*)？然后使用适当数量的参数简单地调用 *loggableN* 方法:

```
case class Complex(real: Double, imag: Double)
implicit val complexLoggable: Loggable[Complex] = new Loggables {}.loggable2(Complex)
"test complex" !! *Complex*(1, 0)
```

但是，如果你自己开发的类不是产品呢？那么你有两个选择。快速和肮脏的版本，你用！|操作员。这将把 *toString* 应用于该值并使用它。更好的方法是在 *X* 的伴生对象中创建一个 *Loggable[X]* 的隐式值，如果您要记录诸如 *Try[X]* 或 *List[X]* 之类类型的值，这也是必需的。这很容易做到，在*自述文件*中有描述。

你可以在 https://github.com/rchillyard/Flog 找到这个日志库。您可以使用下面的 *sbt* 指令将 flog 包含在您的项目中:

library dependencies+= " com . phasmidsoftware " % " flog _ 2.13 " % " 1 . 0 . 8 "

编辑 2021/9/10，添加 *sbt* 依赖信息。