# iOS 中的 Zendesk 帮助中心

> 原文：<https://medium.com/codex/zendesk-help-center-in-ios-d4971fd84096?source=collection_archive---------7----------------------->

## 使用 Zendesk Swift API 提供商

![](img/a04df1d8f3ea04851c2a7593339a5b2b.png)

由[凯利·西克玛](https://unsplash.com/@kellysikkema?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/help?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Zendesk 是一个很好的工具，可以为我们的用户提供一个票务系统，以及一个知识库文章。常见问题。根据我在 iOS 中实现它的经验，`Zendesk`提供了 UI 组件，允许我们在项目中快速包含一个帮助中心。然而，当我们想让 UI 看起来不一样时，事情会变得更复杂，因为修改样式或简单地拥有不同的自定义行为既不简单也不可伸缩。在本文中，我将展示如何使用`Zendesk`服务提供者来构建一个顶层，并分离支持系统关注点。

当然，创建一个新层的好处是显而易见的，它可以定制用户界面，并在我们的用户支持系统的行为方式上有更多的灵活性。另一方面，实现起来需要花费一些时间和精力。但这正是我来这里的目的，我向你展示了你可能需要与这个特性实现一起使用的`Zendesk Service Provider`API。

> 在我们开始学习教程之前，先声明一下。我和 Zendesk 没有任何关系。我得到的所有信息和代码都来自他们公开的[开发者网站](https://developer.zendesk.com/documentation/classic-sdks/support-sdk/ios/help_center/)。本文提供了一个真实的项目示例，可以帮助其他开发人员快速实现相同的目标。

# 让我们先把协议装箱

在过程的早期使用协议的想法是为了有更多的可测试性，并且容易改变提供者，想象一下明天我们决定不再使用 Zendesk，实现新的提供者所需的改变将对项目产生最小的影响。

## 支持提供商

这个协议提供了一种初始化库的方法，这看起来可能没什么用，但是对于测试来说却很方便。

## 支持帮助中心提供商

这个协议将提供 FAQ 逻辑，列出所有文章，搜索，并投票否决或投票支持一篇文章。当然，还可以添加更多的功能，但这是基本的方法。

## 支持请求提供商

该协议管理与用户请求相关的所有逻辑，例如创建请求、获取请求、列出请求的注释以及读取请求的注释。术语`UserRequest`也被称为`UserTicket`，但是为了这篇文章的缘故，也为了让它接近 Zendesk 所提供的，我也称它为`Request`。

# 具体实施

好吧！有了我们的协议，是时候添加我们具体的 Zendesk 实现了，其中我们将使用库提供的东西。由于我们使用协议来规定该功能的行为方式，所以与 Zendesk 或任何其他票务系统相关的所有逻辑都将保持隔离，并且易于跟踪测试、调试和替换(如果需要的话)。

## 初始化 Zendesk

这就是我们如何设置 Zendesk 库，正如你在下一个片段中看到的，这段代码将从`AppDelegate`或你使用的任何依赖注入对象中使用。

注意，我把`supportProvider`对象当作一个`SupportProvider`类型，尽管我把它实例化为一个`ZendeskSupportProvider`对象，这是根据协议而不是具体实现来说的。

## Zendesk 支持帮助中心提供商

## Zendesk 支持请求提供商

## 把所有的放在一起

在这个 [GitHub 资源库](https://github.com/cristhianleonli/zendesk-help-center)中，您可以找到整个项目，包括本文中没有提到的模型。如果你想更深入地研究文档，请访问官方的 Zendesk 开发者网站。

*感谢阅读。我希望你喜欢这个小教程，如果它对你有用，不要害羞👏关于这篇文章。下次见。*