# 协调员，返回按钮问题和一个简单的方法来解决它

> 原文：<https://medium.com/codex/coordinators-the-back-button-and-how-to-solve-it-d336877a6d29?source=collection_archive---------0----------------------->

# 背景

最近，协调器模式在处理 iOS 应用程序导航时变得越来越流行，而不需要紧密耦合不同的视图控制器。

有很多很棒的文章解释了这种模式的各种风格，所以我不会再重复同样的细节。

然而，有一个地方会给这些实现带来问题，这个问题就是`UINavigationController`的后退按钮。

理想情况下，我们应该让协调器直接处理所有的导航交互，但是后退按钮不允许修改它的动作，因为它直接绑定到导航控制器。

当在由协调器管理的第一个视图控制器上按下 back 按钮时，这会导致一个问题。如果没有按钮按下的处理程序，就没有机会将协调器从其父对象中移除，因此会泄漏内存。

Soroush Khanlou 的一篇关于这个主题的博客文章解释了这个问题的两个可能的解决方案:

*   第一个是布莱恩·伊拉斯的方法。这包括实现`UINavigationControllerDelegate`方法来检测视图控制器的弹出，找到其对应的协调器，并在必要时移除它。
*   提到的第二个解决方案是伊恩·麦卡勒姆的方法。这也利用了`UINavigationControllerDelegate`，但是将它包装在一个`Router`对象中，以解耦导航控制器和`Coordinator`。

不幸的是，这两种解决方案都引入了大量的 boiler plate 代码来对按下 back 按钮做出反应。

理想情况下，当一个视图控制器不再需要它的协调器时，这个协调器就会被释放。

# 一种新的解决方案…

我发现解决这个问题的一个办法是颠倒协调者的所有权。通过允许视图控制器保持它们的协调器活动，当最后一个视图控制器被移除时，协调器也被移除。

我们从一个简单的协调协议开始:

从这里我们可以添加一个具体的实现:

这里需要注意的是，第一个视图控制器有意强引用了`InitialCoordinator`。这意味着`SceneDelegate`不需要引用`InitialCoordinator`，所以当视图控制器被释放时，协调器也将被释放。

为了呈现`ChildCoordinator`,我们简单地实例化它并调用它的`start`方法。这和在`SceneDelegate`中启动`InitialCoordinator`是一样的:

# 结论

这个问题的解决方案引入了很少甚至没有锅炉板代码，并利用 Swift 的自动引用计数来处理不再需要的协调器的处置。

在下面的 github repo 中可以找到一个更加完整的项目，包括如何处理模态表示:

[](https://github.com/oconnelltoby/Coordinator) [## 奥康纳·托比/协调员

### 一个没有众所周知的后退按钮问题的协调模式的简单例子

github.com](https://github.com/oconnelltoby/Coordinator)