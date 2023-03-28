# 电子与原生 Windows 框架

> 原文：<https://medium.com/codex/electron-vs-the-native-windows-frameworks-4a7e529af0e5?source=collection_archive---------4----------------------->

## 或者说，你的下一个 app 真的需要电子吗？

![](img/4732bb4e507889a853b6ac63d6510294.png)

[环境元素](https://elements.envato.com/solar-panel-with-blue-sky-ME7TRYT)

Electron 开始被用于许多不同的流行应用程序，如 VSCode 和 WhatsApp，这仅仅是因为它的跨平台特性。但这是电子提供的唯一优势吗？

正如我们将在本文末尾看到的，这不是唯一的好处。我们将会看到，与 C++/C# WindowsForms 和 WPF 等原生框架相比，用 Electron 编写 Windows 应用程序有多简单。为什么只有 Windows？因为绝大多数的程序都是为它而写的，所以对于许多种类的程序(例如游戏)都有一个标准的库栈。

# 显示模态对话框的后台(托盘)应用程序

对于这一类来说，电子可能是多余的。使用 WindowsForms 创建模态对话框很简单，C#也使得最小化窗口和显示它们的通知(托盘)图标非常简单。这也可以在电子中完成，但是[需要多做一点工作](https://stackoverflow.com/questions/37828758/electron-js-how-to-minimize-close-window-to-system-tray-and-restore-window-back)。

# 带有自定义主题的应用程序

因为 WindowsForms 不支持定制窗口主题，所以当开发人员想要为他们的 C#应用程序创建定制主题时，他们使用 WPF 框架。这个过程可能需要很长时间，这取决于主题的复杂程度。

当然，在 Electron 中设计主题也需要时间，但是这得益于大量的 HTML UI 组件，这些组件可以直接从 NodeJS 包管理器`npm`中安装和插入。只有一些 WPF UI 控件可以免费使用，而绝大多数仍然是闭源的、内部的、私有的，远离公众视线。而大多数 HTML UI 组件都是开源的，很容易从 Github 获得。

# 高度集成 API/web 服务的应用

Discord、Telegram 和 Slack 等消息客户端就属于这一类。在 C#上，联网是使用 System.Net 命名空间完成的，或者更常见的是，使用[ASP.NET](https://dotnet.microsoft.com/apps/aspnet)框架。在 C++中，使用了更多的低级套接字函数，这就是为什么复杂的网络传统上是用 C#来完成的。

[System.Net](https://docs.microsoft.com/en-us/dotnet/api/system.net?view=net-5.0)拥有管理 cookies、网络凭证、请求和响应、HTTP 侦听器等的类。如果使用得当，可以开发出强大的支持网络的 C#程序。

C#网络类的一个积极特点是，编译器将检测这些类的大多数不正确用法，这可以节省您运行时调试的时间。对于 Node.js 和 Electron apps 来说，情况就不一样了，所有代码都是在运行时进行评估的，可能会悄无声息地失败。

更糟糕的是(对 electronic 来说)，由于捆绑代码通常会被缩小，因此很难获得与您的源代码相对应的堆栈跟踪。

这并不是说电子不适合构建网络应用。相反，Axios 和 node-fetch 等库很好地处理了网络。由于语言架构的原因，异步请求也(稍微)更容易在 NodeJS 上执行。此外，NestJS 通常用于制作完整的后端 API。

# 视频游戏和其他 GPU 密集型应用

虽然使用 Unity 等 WebGL 游戏引擎构建的 HTML5 游戏开始出现，但由于大量使用 DirectX 和 OpenGL 等图形框架，C#/C++和 WPF 仍然主导着视频游戏行业。特别是，没有简单的方法来集成 Electron 和 DirectX，因为后者是 Windows 特有的技术。

CAD & designer 程序和其他类型的图形加速程序使用 OpenGL 而不是 DirectX，由于电子应用程序可以使用 WebGL，在两种平台上开发这些类型的应用程序的难度大致相同。

# 微软商店应用

这里有一个惊喜:[你可以发布用 Electron](https://www.electronjs.org/docs/tutorial/windows-store-guide) 构建的微软商店应用。它们至少需要 Windows 10 1607 才能运行，并且它们将编译为 AppX 包，在微软手动验证应用程序后，您可以在商店上发布这些包。

所以在这个领域，使用任何一种语言看起来都没有优点或缺点，因为最终产品是一样的——除了 C#可以制作 UWP 应用程序，而 Electron 不能，但公平地说，大多数人都不会在 Xbox 等替代平台上运行 UWP 应用程序。

所有这些的结论是，你是选择用 C#和 WPF 还是用 NodeJS 和 Electron 编写你的应用程序，取决于你是否打算最终将其移植到浏览器，或者你是否想使用 GPU 加速。C#/C++是 GPU 加速程序的明显赢家，而要将你的桌面应用程序部署到网络上，electronic 是必由之路。

将桌面应用程序部署到 web 浏览器有一些用途。例如，如果你使用 electronic 制作一个开源代码编辑器，那么让你在线运行和测试代码的服务可以将你的编辑器嵌入到他们的服务中，供人们在网络上使用。这实际上是 Stackblitz 将 VScode 和 Chromium 浏览器窗口嵌入其 web IDE 的方式。