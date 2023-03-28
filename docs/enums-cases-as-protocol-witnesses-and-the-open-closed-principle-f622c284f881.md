# 列举案例作为协议证人和开闭原则

> 原文：<https://medium.com/codex/enums-cases-as-protocol-witnesses-and-the-open-closed-principle-f622c284f881?source=collection_archive---------10----------------------->

![](img/a3a6f0c073abb6b7514b57117e271d3a.png)

图片由[阿姆扎·安德烈](https://unsplash.com/@andreiamza2000)拍摄

# 背景

最近，我在一个客户的应用程序中实现了许多分析事件。标记这个应用程序(它有大量的 UI)总是一件费力的事情，所以我希望设计一个人机工程学的 API 来与之交互。

在决定接口时，我记得 Swift 5.3 的特性允许 [enum 案例用作协议见证](https://github.com/apple/swift-evolution/blob/main/proposals/0280-enum-cases-as-protocol-witnesses.md)。这成了我正在配对的低年级学生的一个很好的教学机会。简而言之，一个需要返回`Self`的`static`函数的`protocol`可以由一个具有关联值的`enum` `case`来满足。此外，协议中任何类型为`Self`的`static`变量都可以由没有相关值的`enum` `case`来满足。

假设我们有以下协议:

我们可以用下面的结构来满足它:

此外，我们还可以用这个枚举来满足协议:

对于 API 消费者来说，这两者看起来是一样的，因为它们被完全相同地调用:

# 开闭原则

[启闭原理](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)是 5 个[实体](https://en.wikipedia.org/wiki/SOLID)设计原理之一。它声明“软件实体应该对扩展开放，但对修改关闭。”这意味着，我们的 API 应该是可扩展的，而不必改变原始源代码。

# **枚举 vs 结构**

开闭原则是一个容易出错的地方。它们不能被扩展来包含额外的`case`，所以每次一个新的事件类型被添加到我们的协议中，我们都需要修改原始的`enum` s 源。对于可以跨多个文件扩展的`struct`，情况并非如此。

另一个考虑因素是协议的大小。目前有两个事件，但是当要跟踪的事件数量激增时会发生什么呢？如果我们有大约 60 个屏幕，每个屏幕有 2-5 个事件，我们最终会有大约 210 个`enum` `case`，这些都必须放在同一个文件中，对于我们的`name`变量，我们也会有一个巨大的`switch`语句。

在后端分析系统只能处理某些事件的情况下，通过使用`enum`来防止任何未知和未处理的事件溜走可能是完全有意义的，但总的来说，我认为`struct`模式在这种情况下工作得最好。

# 结论

使用`enum` `case` s 作为协议见证是对 Swift 语言的一个很酷的补充，但是我们仍然需要考虑`enum` s 是否是手头工作的最佳工具。和往常一样，当你有了一把闪亮的新锤子，很容易把所有东西都当成钉子，但总是值得后退一步，评估 API &的需求及其持续使用的人体工程学。