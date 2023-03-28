# 设计模式|理解 LLD 的方法

> 原文：<https://medium.com/codex/design-patterns-way-to-understand-lld-38b3a102c627?source=collection_archive---------15----------------------->

![](img/f3fd4c8f32564dde6760f05185ab5ba1.png)

如果你目前正试图为亚马逊、微软或大型科技公司做准备，有三个主题你必须准备清楚。每个人都知道算法和数据结构有多重要。系统设计，换句话说，HLD(高层设计),为此我将强烈推荐 Alex Xu 的这本书。简单且做得很好的解释。和设计模式，即 LLD(低级设计)。通过这些博客，我将尝试传达我对设计模式的理解，并希望能对读者有所帮助。我希望你们熟悉 Python 和它的类。开始吧！

什么是设计模式？

简而言之，您可以基于一般问题的一般解决方案来构建您的代码。这些通用的解决方案被称为设计模式。设计模式是各种软件开发人员经验的积累。他们认为处理问题的最佳方式。设计模式在数量上是多种多样的，但是在我接下来的文章中，我们将只讨论其中最重要的 10 种。先说策略模式。

**战略模式**

先说个例子。它会帮助我让你更好地理解这个概念。

你买了东西，现在你想让它被送到。许多公司都以不同的成本收费来这样做。我们想要的是有一个单一的接口，将处理这些不同的托运人，并计算与他们每个人的运输成本。当我们读到这类问题时，我们首先想到的是 if/else 语句。如果发货人是 UPS，我们会这样做，否则我们会这样做，等等。了解策略模式将增强你的思维，摆脱这些原始的 if/else 语句。

因此，有各种各样的托运人，如联邦快递，UPS，邮政服务，未来可能会有很多。所以我们的解决方案也应该是可扩展的。

首先，我们将创建一个**订单**类。

```
class Order:
    def __init__(self):
        pass
```

请注意，我们的订单类不包含任何内容。将**的单一职责赋予特定的类**是策略模式的一个原则。我们的运输成本类别**不会依赖**订单类别。它们执行两种不同的功能。

现在我们将定义我们的 ShippingCost 类，它将作为这个问题的**上下文**。运费会由它来计算。

```
class ShippingCost:
    def __init__(self, strategy):
        self._strategy = strategy def shipping_cost(self, order):
        return self._strategy.calculate(order)
```

它从各种各样的运输商那里接受了一个策略。并在此基础上计算成本。

为了让不同的发货人有不同的逻辑，我们必须有一个公共的**接口**，在此基础上，我们将构建我们的代码，这样当我们给我们的类一些输入时，将会产生相同类型的输出。为了在 Python 中实现接口，我们使用 ABC 或抽象基类。

```
from abc import ABCMeta, abstractmethodclass AbsStrategy:
    __metaclass__ = ABCMeta @abstractmethod
    def calculate(self, order):
        pass
```

我们的接口告诉我们，每个将从它继承的类必须在它们的定义中有 *calculate* 方法。

然后，我们将在基于我们的接口的各自的类中为不同的发货商定义逻辑/算法。

```
class FedExStrategy(AbsStrategy):
    def calculate(self, order):
        return 3.00class PostalStrategy(AbsStrategy):
    def calculate(self, order):
        return 5.00class UPSStrategy(AbsStrategy):
    def calculate(self, order):
        return 4.00
```

你可以看到这个**是如何扩展的**。如果你想添加另一个发货人，你只需要添加它自己的类。你不需要在我们的**上下文**中进行修改，即 ShippingCost 或者我们的**接口**或者在任何地方使用 if/else，就像我们已经处理了**打开/关闭原则**一样。

> 驱动程序代码

```
# Test FedX servicesorder = Order()
strategy = FedExStrategy()
cost_calculator = ShippingCost(strategy)
cost = cost_calculator.shipping_cost(order)
assert cost == 3.00# Test UPS shippingorder = Order()
strategy = UPSStrategy()
cost_calculator = ShippingCost(strategy)
cost = cost_calculator.shipping_cost(order)
assert cost == 4.00# Test POST shippingorder = Order()
strategy = PostalStrategy()
cost_calculator = ShippingCost(strategy)
cost = cost_calculator.shipping_cost(order)
assert cost == 5.00print("All test passed\n\n\n")
```

你可以看到不同的成本是如何通过不同的策略或政策计算出来的。这就是为什么这种模式也被称为**策略模式**。

*不同种类的逻辑/算法被封装，可以互换使用，如我们的驱动程序代码中的* ***策略变量*** *所示。*

ShippingCost 类将采用一个策略，并从它自己的私有方法“shipping_cost”中调用它们的 calculate 方法。

您可以将上述代码复制到一个文件中。浏览并运行它。

我们对策略模式的讨论到此结束。希望您能看到这一点的用处，并利用它来改进您的代码结构，给每个人留下深刻印象！下次见。