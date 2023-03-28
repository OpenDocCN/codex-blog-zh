# C# dotnet Core 6 —使用 SignalR 和 Blazor 的实时应用程序。面向企业家的 Visual Studio 编码

> 原文：<https://medium.com/codex/c-dotnet-core-6-real-time-app-using-signalr-with-blazor-visual-studio-coding-for-entrepreneurs-899e9ccedcf6?source=collection_archive---------7----------------------->

![](img/ac4ad3fb366b71730d644364f777a88f.png)

欢迎来到 StartupHakk！今天我们将继续我们的企业家编码系列。我是一个初创公司的创始人和开发者，我知道这两者之间的完美平衡。因此，我正在做一系列的编码教程，包括容易启动的项目，让你更容易开始你的业务，并建立你需要的东西。或者，如果你只是为了更有效地管理开发人员和/或承包商而学习——太好了！不管你学习编码的原因是什么，我知道这只会让你的生意更好。所以让我们开始吧！

今天我们将学习更多关于布拉索的知识。我们将使用 Visual Studio 在前端构建一个使用 Blazor 的应用程序，并在后端构建一个 SignalR 应用程序。然后我们将浏览一些例子。

本教程教授使用 SignalR 和 Blazor 构建实时应用程序的基础知识。

了解如何:

*   创建一个 Blazor 项目
*   添加 SignalR 客户端库
*   添加信号集线器
*   为 SignalR hub 添加 SignalR 服务和端点
*   为聊天添加 Razor 组件代码

在本教程结束时，你将有一个工作的聊天应用程序。

# 使用 C#的交互式 web 用户界面

Blazor 允许你使用 C#而不是 JavaScript 来构建交互式 web 用户界面。Blazor 应用程序由使用 C#、HTML 和 CSS 实现的可重用 web UI 组件组成。客户端和服务器端的代码都是用 C#编写的，允许你共享代码和库。

Blazor 是 ASP.NET 的一个特性，这个流行的网络开发框架扩展了 T2。NET developer platform 提供了构建 web 应用的工具和库。

# 在 WebAssembly 或服务器上运行

Blazor 可以使用 WebAssembly 直接在浏览器中运行你的客户端 C#代码。因为它是真实的。NET 运行在 WebAssembly 上，您可以重用应用程序服务器端部分的代码和库。

或者，Blazor 可以在服务器上运行您的客户端逻辑。客户端 UI 事件使用 SignalR(一种实时消息传递框架)发送回服务器。一旦执行完成，所需的 UI 更改将被发送到客户机并合并到 DOM 中。工具提示:文档对象模型(DOM)是一个编程接口，它将 HTML 页面上的所有元素表示为树结构中的节点。使用 DOM，可以在页面中更新、添加和删除元素。

[](https://dotnet.microsoft.com/en-us/apps/aspnet/web-apps/blazor) [## 用 C# |构建客户端 web 应用程序。网

### Blazor 可以使用 WebAssembly 直接在浏览器中运行你的客户端 C#代码。因为它是真实的。网络运行在…

dotnet.microsoft.com](https://dotnet.microsoft.com/en-us/apps/aspnet/web-apps/blazor) 

这最终取决于您正在构建的技术，但在本系列中，我们将继续努力。Net/。网芯。为此，您可以使用 Microsoft Visual Studio 或 Microsoft Visual Studio 代码。这两款都可以在 Windows 和 macOS 的官网上免费下载。

两个都可以下载，看看哪个适合你的需求。在本视频中，我们将创建一个新项目。它可以作为熟悉 visual studio/code 不同方面的指南。

# Visual Studio 与 Visual Studio 代码

您可以将 Visual Studio 看作是一个千篇一律的解决方案，其中包含了各种特性和功能。由于 Visual Studio 功能更强大，它可以在大多数情况下开箱即用，而 Visual Studio 代码可以被视为它的精简版，有许多可用的插件，最终可以使两者几乎相同。有些人甚至认为它是一个“美化的文本编辑器”。相比之下，Visual Studio 确实需要更多的知识来发挥其真正的潜力，不像 Visual Studio 代码更适合初学者，但是，Visual Studio 确实提供了对一切更精确的控制。归根结底，这完全取决于个人喜好。