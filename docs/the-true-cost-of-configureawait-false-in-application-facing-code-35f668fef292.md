# 面向应用程序的代码中 configurewait(false)的真实开销。

> 原文：<https://medium.com/codex/the-true-cost-of-configureawait-false-in-application-facing-code-35f668fef292?source=collection_archive---------3----------------------->

## 使用 configurewait(false)并不是提高 WPF 应用程序性能的好方法。

![](img/3703628cf77a97a46f8d7916db7149ed.png)

照片由[克里斯蒂娜@ wocintechchat.com](https://unsplash.com/@wocintechchat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

过去一年我一直在开发一个 WPF 应用程序，最近，我开始注意到一个有趣的函数`ConfigureAwait(false)`出现在异步函数调用中。有人告诉我这是为了提高应用程序的性能，所以我想我应该更深入地研究一下这个简单的函数调用究竟是如何提高性能的。在所有有趣的阅读深入到像`SynchronizationContext`这样的概念和`async/await`如何在幕后工作之后，我得出的结论是`ConfigureAwait(false)`不仅是不必要的，而且在面向应用的代码中是危险的。在这篇文章中，我解释了为什么…

> `ConfigureAwait(false)`在面向应用的代码中不仅是不必要的，而且是危险的。

# 同步上下文

在我们进入讨论的实质之前，重要的是我们要探索一个对异步编程至关重要的构造。Net，一个叫做`SynchronizationContext`的类。简单地说，`SychronizationContext`代表代码可能运行的位置。它通过公开一个接受委托运行的`Post`方法来做到这一点。

在异步编程中，当我们将一个工作单元委托给另一个线程时，我们将剩余的代码捕获为一个延续块。这样做是为了如果我们需要控制延续块在哪里运行，我们可以使用`SynchronizationContext`到`Post`延续块到我们希望它运行的任何位置。UI 框架经常创建自己版本的`SynchronizationContext`来覆盖`Post`方法。这使得它们能够确保需要在特定线程上运行的特定代码在该特定线程上运行。WPF 实现了一个`DispatcherSychronizationContext`，它覆盖了`Post`方法来调用`Dispatcher.BeginInvoke`来运行延续块。这确保了在 UI 线程上开始的代码在 UI 线程上继续。

# Async/Await 和 SychronizationContext

我们需要理解的另一个重要概念是`async/await`以及它与`SychronizationContext`的关系。简单地说，`async/await`提供了异步编程的抽象，允许你写异步代码，读起来像普通的同步代码。这是通过将在`await`关键字之后运行的代码注册为由`await`语句返回的`Task`对象的延续来实现的。也就是说，在异步程序中，当我们将一个工作单元委托给另一个线程时，我们首先通过将当前环境存储在`SycnhronizationContext`的一个实例中并将其放在`Task`对象上来捕获它。在运行延续块之前，我们首先检查是否有与`Task`相关联的同步上下文。然后我们使用`SynchronizationContext`的`Post`方法运行 continuation，如果它存在，或者在异步代码被处理的位置运行它。默认情况下，线程池中的线程有一个为`null`的`SychronizationContext`，UI 线程有一个被设置为`SynchronizationContext`类的某个实现的`SynchronizationContext`。这是因为 UI 线程通常是唯一被允许操作 UI 的线程。因此，任何操纵它的代码都需要在 UI 线程上运行。在 UI 线程上设置`SynchronizationContext`使我们能够从任何其他线程中为 UI 线程编写`Post`代码。如果只能在 UI 线程上运行的代码在不同的线程上运行，则会抛出异常`System.InvalidOperationException: 'The calling thread cannot access this object because a different thread owns it.'`。

我在[这篇文章](https://hamidmosalla.com/2018/06/24/what-is-synchronizationcontext/)中找到了关于`SynchronizationContext`和`asyc/await`如何联系的最佳解释。它提供了异步编程如何在 C#中工作的一个简化版本，而没有像`async/await`那样的语法糖。

## 什么是 ConfigureAwait？

在不深入细节的情况下，`ConfigureAwait(continueOnCapturedContext: false)`是一个用结构`ConfigureTaskAwaitable`和为`continueOnCapturedContext`传递的布尔值包装等待的`Task`对象的方法。这意味着不是在原始的`Task`对象上调用`Task.GetAwaiter`(`async/await`模式的一部分)，而是在包装对象上调用`GetAwaiter`方法。这提供了一种方法来改变 awaiter 如何注册`Task`对象的延续块的默认行为。下面显示了上述代码如何被一个`if`语句包装的简化版本，该语句使用传递给`ConfigureAwait`的`continueOnCapturedContext`参数进行评估。这意味着通过使用`ConfigureAwait(false)`，延续块在处理异步代码的线程上运行，而不管是否有捕获的`SynchronizationContext`。

# configurewait(false)如何提高性能？

在他的优秀文章回答关于`ConfigureAwait`的常见问题中，斯蒂芬解释说`ConfigureAwait(false)`可以通过两种方式提高性能。如果上面的`RestOfMethod`方法中的代码不需要在 UI 线程上运行，那么…

1.  使用`Post`方法可以节省注册回调的成本。这是因为注册回调比简单地调用它成本更高。
2.  我们可以对`RestOfMethod`的运行方式进行运行时优化，因为它是被直接调用的，而不是被注册为回调。如果`RestOfMethod`的运行是由某个任意抽象的实现来处理的，那么应用运行时优化是很困难的。当我们确切知道回调将如何被调用时，可以做更多的优化。

另一种节省性能的方法是，如果`RestOfMethod`包含一些长时间运行的阻塞代码，我怀疑这是大多数人使用它的原因。考虑下面包含`SomeLongRunningBlockingMethod`的`RestOfMethod`的实现。

如果`RestOfMethod`不需要在 UI 线程上运行，那么 UI 线程会在`SomeLongRunningBlockingMethod`的持续时间内被不必要地阻塞，从而导致应用程序感觉“缓慢”和用户体验不佳。

# **那么 configurewait(false)有什么问题呢？**

在像 WPF 这样的 UI 框架中使用`ConfigureAwait(false)`的主要问题是，它违反了软件开发的基本原则之一；接吻(简单点，傻逼)。当编写应用程序时，尤其是使用框架时，通常希望保持默认行为(这就是为什么它是默认行为)。偏离默认行为可能会产生意想不到的副作用，最终导致可能比您最初打算解决的问题代价更高的问题。在`ConfigureAwait(false)`的例子中，这里有一个到目前为止我看到的问题列表。

## 它不一定做你可能认为它做的事情。

也许`ConfigureAwait(false)`最狡猾的地方在于，它不一定保证`await`关键字之后的代码不会在最初捕获的上下文中运行。考虑下面`SomethingAsync`的实现。

尽管在一个等待的任务上使用`ConfigureAwait(false)`保证了延续不会排队回到原始上下文中，但这并不能保证延续不会在原始上下文中运行。在一个已经完成的 any 上调用`await`会导致代码在`await`之后同步运行，而不会强制任何东西在任何上下文中排队。这意味着通过以上`SomethingAsync`的实现，`RestOfMethod`以及`SomeLongRunningBlockingMethod`将在 UI 线程上运行。抓住你了。

## 这使得代码僵化而脆弱

下一个关于`ConfigureAwait(false)`的问题来自于大多数人首先使用它的原因。上面的讨论已经非常清楚地表明了这样一个事实:当且仅当`RestOfMethod`不需要在 UI 线程上运行时，`ConfigureAwait(false)`才会工作。这给了开发人员额外的脑力任务，即跟踪哪个线程`RestOfMethod`需要运行。应用程序逻辑的简单改变可以导致在`RestOfMethod`中发生一些 UI 操作，或者甚至在由`RestOfMethod`调用的方法调用的方法中发生(深度嵌套的函数调用并不少见)。这迫使开发人员要么通过强制执行一条规则来使代码变得僵化，这条规则是:“*任何 UI 操作代码都不应放在* `*RestOfMethod*` *中”，要么通过让开发人员决定`RestOfMethod`中的某段代码是否需要在 UI 线程中运行来使代码变得脆弱，如果需要，则移除`ConfigureAwait(false)`或找到另一种变通方法。这是一个很大的额外的精神负担，当我们可以遵循 KISS 设计原则并使用默认行为时，这很可能会导致错误和 UI 损坏！*

## 有一个更好的方法来解决你的性能问题

你可能会问自己如何用运行`SomeLongRunningBlockingMethod`的 UI 线程解决上面的问题，那么使用`ConfigureAwait(false)`是不是一个坏主意。还好，`Task` API 为这个问题提供了一个简单的解决方案。考虑下面`RestOfMethod`的实现。

这个实现使用静态方法`Task.run`在线程池中运行`SomeLongRunningBlockingMethod`。如果需要的话，甚至可以通过简单地将`RestOfMethod`转换为`RestOfMethodAsync`来等待`SomeLongRunningBlockingMethod`的返回值。我认为这个解决方案更清楚地说明了代码的意图，以及在不脱离默认实现`async/await`的情况下应该在哪里运行。

# 结论

> 如果你正在编写应用级代码，不要使用`ConfigureAwait(false)`。

我理解提高应用程序性能的需要，但我不认为使用`ConfigureAwait(false)`是这样做的正确方法。它引入了不必要的精神负担和可能破坏用户界面的潜在陷阱。此外，它要解决的问题可以很容易地解决，而不需要脱离`async/await`的默认行为，即一旦`Task`完成，就将控制返回给调用线程。我认为 Stephen 在他的文章中说的很对，他说**如果你正在编写应用程序级代码，那么*不要使用*** `ConfigureAwait(false)`。

你觉得`ConfigureAwait(false)`怎么样？请随时与我进一步讨论这个话题。我很乐意接受更多这方面的教育😅