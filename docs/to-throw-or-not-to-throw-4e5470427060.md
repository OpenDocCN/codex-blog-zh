# 扔还是不扔

> 原文：<https://medium.com/codex/to-throw-or-not-to-throw-4e5470427060?source=collection_archive---------1----------------------->

我经常发现团队说服自己不使用异常进行流程控制，因为他们不做像沃德·坎宁安的[维基](https://web.archive.org/web/20140430044213/http://c2.com/cgi-bin/wiki?DontUseExceptionsForFlowControl)中描述的`ResultException`这样的构造。

在大多数这样的团队中，事情可能没有那么糟糕。

但是我已经看到异常以非常接近流动控制的方式被使用，特别是当所述**异常的存在被用来触发复杂的业务场景**时。

你可能也看到了这确实是一个经常使用的方法，那么这样使用异常有什么不好呢？

## 1.例外会增加认知的复杂性

在你的代码中大量使用异常会使代码难以阅读和理解。记住每一个`catch` [都会增加](https://www.sonarsource.com/docs/CognitiveComplexity.pdf)认知的复杂性。

谈到异常，我个人的偏好总是这样:异常应该只在发生异常时使用。换句话说，**只有当你需要从已经发生的不好的事情中快速退出时才抛出异常。**

![](img/bebb20282ac914237226d2bb4918cc4c.png)

一个快速离开大楼的出口【图片来自[Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1040527)

*例如，如果正在执行的业务功能是一个捕获支付的功能，并且如果履行该功能所需的一些依赖关系不可用(如外部支付网关关闭)，则抛出异常。*

另一个解决方案是完全避免异常，选择另一种机制，如`Result` [对象](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-result/)或[单子](https://www.freecodecamp.org/news/a-survival-guide-to-the-either-monad-in-scala-7293a680006/)

然而，我还没有看到每个团队都渴望采用像`Result` 对象这样的新概念。此外，考虑到移植现有代码并不容易。

这让我开始思考。有没有一套我们可以遵循的准则，使得代码可读性更好，同时仍然使用异常？

因此，我试着列出一份清单。

# 基本准则

## 1.处理来自外部库的异常

在可能抛出异常的地方附近处理从第三方库中抛出的异常。不要在下游处理异常。

如果我们让第三方抛出的异常传播到代码的所有层，它会增加与库的耦合。

如果需要的话，当您将库切换到另一个库时，这也可能会引发微妙的、无法捕捉的错误。

## 2.尊重建筑边界

这与上面的问题非常相似，但我将它作为一个单独的点来提及，因为这是我经常看到的事情。

例如，避免在表示层处理从数据库层抛出的特定于数据库的异常。

## 3.处理您自己的异常

如果您自己在代码中抛出异常，只需处理一次。

这应该只在一个地方完成——如果你来自 Spring 世界，希望是一个专用的`ExceptionHandler`类。

这个建议的原因是由于异常的本质，它们可以作为**非本地化的** `**goto**` **语句。**

因此，我想降低它可能引发的问题的等级，只在一个地方查看异常的处理。对于未来的维护者来说，这比必须通过完整的执行链来理解发生了什么更容易。

这种范式也可能引发一些有趣的问题需要解决。

我们经常需要捕捉我们自己抛出的异常，可能更新一两个指标，或者一个数据库实体，并重新抛出相同的或者(甚至)不同的异常，以便下游的一些代码可以处理它。

有一些方法可以做到上述事情，这些方法可能包括首先不使用异常，或者甚至修改代码架构，以便满足只处理一次抛出的异常的范式。

这通常并不容易，但将来当下一个人试图阅读和解读正在发生的事情时，这将会有所回报。

## 4.记录 API 方法

如果您正在编写一个抛出未检查异常的方法，请记录下来。简单的`JavaDoc`评论就能达到目的。**一个不记录它抛出的关于其契约的异常的方法。**

帮助未来的代码维护者。如果他们没有这份文件，可能是不知道它`throws`是不愉快路径情况下的一个例外。

通常情况下，他们要么

*   用`try`和`catch`块包装所有调用该方法的代码，使代码不必要地冗长(or)
*   忘记捕捉异常，并可能结束将异常传播到下游层。

两者都是糟糕的地方。

# 结论

这些都是我在最近的一个项目中想到的非常基本的观点，也是我在思考如何编写更易于维护和阅读的代码的时候想到的。

# 参考

1.  什么是认知复杂性—[https://www.sonarsource.com/docs/CognitiveComplexity.pdf](https://www.sonarsource.com/docs/CognitiveComplexity.pdf)
2.  Scala 中的单子——[https://www . freecodecamp . org/news/a-survival-guide-to-the-any-monad-in-Scala-7293 a 680006/](https://www.freecodecamp.org/news/a-survival-guide-to-the-either-monad-in-scala-7293a680006/)
3.  Kotlin 结果对象—[https://kot linlang . org/API/latest/JVM/stdlib/kot Lin/-Result/](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-result/)