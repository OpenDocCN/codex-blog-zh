# Swift —可选的是什么？以及如何使用它

> 原文：<https://medium.com/codex/swift-optional-a09dcb2432d9?source=collection_archive---------7----------------------->

![](img/fc5cc38c95652a3ab8092fb78d88e086.png)

Swift 语言给了我们一个概念叫做**可选**。
**可选**并不新鲜，几年前就有了(由 Haskell 等语言，一个叫做[也许是](https://wiki.haskell.org/Maybe)的对象)。
如果您刚接触 Swift，这个概念可能会显得陌生或不直观，但**可选的**有其优势，我们将在这里解释它们。

**什么是可选的？** 对于**可选的正确定义，**是一个有两种情况的**枚举**，这两种情况是:

其中 none 表示该值为空—零，some 表示该值实际上包含某些内容。
瞧——这就是隐藏在我们称之为**可选的怪异新事物下的惊喜。**

更清楚地说，一个**可选的**是围绕值的包装器，这个包装器可以是两个中的一个，或者是空的**(。无)**，或者里面有东西**(。一些(包好的))。**

假设开发者给一个**可选的**赋值，这个基本构造函数将被调用，这个构造函数创建一个新的**枚举**，开发者将赋值设置到**中。某**案。

另一方面，如果开发者给一个**可选值赋了一个空值，那么**构造器将创建一个**枚举**和**。无**案例。在上面的截图中，我们可以看到**可选**符合**expressibybynilliteral**协议，这就是为什么它允许我们在**可选**中设置一个空值。

# **展开**

那么，现在我们明白了什么是**可选**了，我们该如何使用它呢？
有几种方法可以使用**可选的**值。

**无条件展开** 无条件展开可以通过使用字符'！'来实现。通过使用无条件解包，我们说:“是的，我 100%确定那个**可选**中有一个值，我想使用它”，例如:

**可选解包** 另一种解包**可选**值的方法是在使用实际值之前检查它是否存在，例如:

Swift 3.0 向我们介绍了可选的解包，它基本上与上面的片段相同，只是语法看起来更好，例如:

上面的代码片段需要使用“！”标记是为了解开**可选，**因为实际值在`unwrappedString`里面，`unwrappedString`不是**可选**而是一个实际的字符串。

**Guard** 

# **结论**

Swift 向我们介绍了一种使用值的新方法，可选的**是一个强大的对象。开发人员可能会感到困惑，因为使用无条件解包很容易忽略可选的，但是当正确使用可选的时，它会使代码安全、易于维护和可读。**

这是关于 Swift 中**可选**的简要说明，进一步阅读你可以在这里找到[的](https://developer.apple.com/documentation/swift/optional)