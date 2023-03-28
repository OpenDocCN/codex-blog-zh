# 为什么你应该使用协议。

> 原文：<https://medium.com/codex/why-you-should-be-using-protocols-d72b03a0fff3?source=collection_archive---------5----------------------->

![](img/19580c284c0dc770293349a45a38dd36.png)

亚历克斯·巴科尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

作为一名 iOS 开发人员，您可能已经在您所做的项目或您所关注的其他项目中遇到过协议。但是为什么开发人员要使用协议呢？它们到底是做什么的，为什么有用？今天，我们将深入研究协议，并学习如何在您的下一个 Swift 项目中使用它们！

协议用于为 Swift 中的对象创建核心结构。这种结构类似于类的结构，但是主要的区别之一是你只定义了*什么*功能与一个协议相关联，而不是功能本身。为了更好地理解，请看下面的例子:

```
protocol Routine {
   wakeUp()
   getCoffee()
   goToWork()
}
```

上面的例子创建了一个名为“Routine”的协议。在这个协议中，我们已经声明，任何类型为“Routine”的对象都必须具有 wakeUp、getCoffee 和 goToWork 函数。从这个协议声明中可以看出，我们并没有像在类中那样创建函数本身，我们只是简单地告诉 Swift“嘿，将会有 Routine 类型的对象，其中至少包含这三个函数”。

您可以在我们的代码中看到一个如何使用上面创建的协议的示例:

```
class MondayRoutine: Routine {
   func wakeUp() {
      print("...turning off alarm")
   } func getCoffee() {
      print("Grabbing a cup of coffee")
   } func goToWork() {
      print("Driving to work")
   }
}
```

在这个例子中，我们创建了一个名为“周一例行程序”的类，它符合“例行程序”协议。为了使这个类符合“常规”协议，我们必须包含这个协议中所需的三个函数。与在协议中不同，在类本身中，我们创建了函数体。

如果我们想创建另一个符合“常规”协议的类，我们可以像下面这样做:

```
class TuesdayRoutine: Routine {
   func wakeUp() {
      print("...overslept alarm")
   } func getCoffee() {
      print("Pour two cups of coffee")
   } func goToWork() {
      print("Take the bus to work")
   }
}
```

我们现在已经创建了另一个符合“常规”协议的类。如您所见，TuesdayRoutine 将打印出与 MondayRoutine 不同的语句，但它们都是一个例程，因为它们包括 wakeUp、getCoffee 和 goToWork 函数。

那么为什么要使用协议呢？协议对于在不提供实现的情况下形式化代码之间的连接非常有用。它们允许我们在代码中提供结构，而无需将不同的系统组件紧密地联系在一起。

如果您正在构建一个大型项目，并且知道您将需要为将要多次创建的特定类型的类提供四个不同的函数，那么这将是一个开始实现协议的好地方。它允许你的代码有很好的可重用性。协议还让我们放心，我们创建的任何类都必须至少具有我们在协议声明本身中声明的功能。

关于协议，另一个需要了解的重要事情是，它们是一种快速的类型。这意味着您可以像使用 Swift 中的其他*类型*一样使用它们。你可以在下面看到一个例子:

```
func weeklyRoutine(days: [Routine]) {
   for day in days {
      day.wakeUp()
      day.getCoffee()
      day.goToWork()
   }
}
```

此示例创建一个函数，该函数采用一个例程类型对象数组。然后，该函数遍历数组并调用我们知道的日常生活中包含的每个函数，因为它们属于 Routine 类型。

希望在阅读本文后，您对 Swift 中的协议以及它们如何在您的代码中有用有了基本的了解。我鼓励你在下一个项目中尝试协议，看看它们能给你带来什么好处！