# 关于 Node.js 的所有事情

> 原文：<https://medium.com/codex/all-things-about-nodejs-12225bf6be63?source=collection_archive---------15----------------------->

## 这是什么？它是用来做什么的？怎么学？还有更多…

![](img/0319e9e9e75829904fe9de3af823723d.png)

斯科特·韦伯在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# Node.js 简介:

**Node.js** 是一个**开源**，**跨平台** *运行时环境*，用于开发**服务器端**和网络应用。构建**和* ***高效*** REST API 是当今最流行的技术之一。 *Node.js* 应用用 **JavaScript** 编写，可以在 **z/OS、Linux、macOS、Microsoft Windows、SmartOS、FreeBSD、OpenBSD、IBM AIX 上的 *Node.js* 运行时内运行。***

*它允许 **Node.js 开发者**使用 **JavaScript** 和*处理各种核心功能的模块集合*创建 **web 服务器** & **网络工具**。*

*在本文中，我们将讨论 node.js。*

# *node.js 的开头:*

*![](img/f2c0ce505a88bd39480d0200a127aca1.png)*

*[Jukan Tateisi](https://unsplash.com/@tateisimikito?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片*

*Dahl 创造了这种语言，因为他不喜欢 Apache http server 处理多个并发连接的方式。事实上，他批评了代码阻塞整个进程的方式，或者暗示多个执行堆栈用于同时连接的方式。他的强烈愿望促使他创建了 Node.js 项目，并在 2009 年 11 月 8 日举行的首届欧洲 JSConf 大会上展示了这个项目。Google 的 [V8 JavaScript 引擎](https://v8.dev/)、事件循环和低级 I/O API 在项目中的巧妙运用成功赢得了数百万人的芳心。*

*2011 年 6 月***微软**和[Joyent](https://www.joyent.com/)共同实现了 Node.js 的原生 **Windows** 版本，第一个版本于 2011 年 7 月发布。
2012 年 1 月， [**达尔**](https://en.wikipedia.org/wiki/Ryan_Dahl) 让位&提拔同事、 **npm** 创建者“Isaac Schlueter”管理项目。**

# *Node.js 如何在幕后工作？*

*JavaScript 和 Node.js 都运行在 **V8** JavaScript 运行时引擎上。这个 V8 引擎将 JavaScript 代码转换成更快的机器代码。*

*V8 是最初为谷歌 Chrome 打造的 JavaScript 执行引擎。2008 年，谷歌将其开源。V8 用 C++编写，在运行时将 JavaScript 源代码编译成本机代码。截至 2016 年，它还包括 Ignition，一个字节码解释器。*

# ***为什么要用 node.js？***

*如果你是一名 web 开发人员，你可能会看到 node.js 在开发人员中如此受欢迎，但你可能不知道 node.js 为什么如此受欢迎！？*

*有一些重要的特性使 Node.js 成为软件架构师的首选:*

*   ***非常快:**node . js 库建立在 Google Chrome 的 V8 JavaScript 引擎上，代码执行速度非常快。*
*   ***单线程但高度可伸缩:** Node.js 使用单线程模型，带有事件循环。事件机制有助于服务器以非阻塞的方式做出响应，并使服务器具有高度的可伸缩性，这与创建有限线程来处理请求的传统服务器不同。Node.js 使用单线程程序，同 Apache HTTP Server 等传统服务器相比，同一个程序可以为更多的请求提供服务。*
*   ***事件循环:**事件循环允许 node.js 执行非阻塞 I/O 操作，尽管具有单线程特性。通过节点的事务遍历一系列回调。并且使用' ***libuv*** '库( **Libuv** 是一个 **C** 库，它实现了这个模式，并且是 Node.js 核心模块的一部分。你可以在这里阅读更多关于 libuv [的内容。)，它处理队列&处理异步事件。](https://nikhilm.github.io/uvbook/introduction.html)*
*   ***无缓冲:** Node.js 应用程序从不缓冲任何数据。这些应用程序简单地**成块输出数据**。因此用户可以不受干扰地轻松观看视频或流。*
*   ***轻量级且易于扩展:**相应地，将**应用逻辑**分解为*较小的模块*、*微服务*，而不是创建一个*单个*、*大型*单片内核，您可以实现更好的灵活性，并为进一步发展奠定基础。因此，在现有服务的基础上添加更多**微服务**比将附加功能与基本应用功能相集成要容易得多。*
*   ***社区驱动:**回到易访问性的话题，你会意识到最易访问的软件通常拥有最大的社区。大型社区的好处是大量的支持和反馈。*
*   ***许可证:** Node.js 是在 [MIT 许可证](https://raw.githubusercontent.com/joyent/node/v0.12.0/LICENSE)下发布的。*

# *从哪里学 Node.js？*

*学习的方法有很多，你可以看书，看视频等等。了解你喜欢学习的方式。以下是学习 Node 的好资源。JS:*

*   *[**W3school.com**](https://www.w3schools.com/nodejs/)*
*   *[**nodejs.dev**](https://nodejs.dev/learn)*

## *Udemy:*

*   *[**【node . js 开发者教程全集(第三版)**](https://click.linksynergy.com/fs-bin/click?id=JVFxdTr9V80&subid=0&offerid=323058.1&type=10&tmpid=14538&RD_PARM1=https%3A%2F%2Fwww.udemy.com%2Fthe-complete-nodejs-developer-course-2%2F)*
*   *[**【NodeJS】—完整指南(MVC、REST APIs、GraphQL、Deno)**](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fcourse%2Fnodejs-the-complete-guide%2F)*
*   *[**节点 JS:高级概念**](https://click.linksynergy.com/deeplink?id=JVFxdTr9V80&mid=39197&murl=https%3A%2F%2Fwww.udemy.com%2Fcourse%2Fadvanced-node-for-developers%2F)*
*   *[**node . js 简介**](https://pluralsight.pxf.io/c/1193463/424552/7490?u=https%3A%2F%2Fwww.pluralsight.com%2Fcourses%2Fnode-intro)*

# *使用 Node 的公司。JS:*

*![](img/43eae01adee1494c6407ce82a84ccbbc.png)*

*弗洛里安·维塔在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片*

****Node.js 正日益成为全球多家公司的首选技术*** 。由于 Node.js 强调并发性、速度和密集的数据交换，并在 web sockets 上采用推送技术，它已经帮助公司构建了各种应用程序，如社交媒体应用程序、视频和文本聊天引擎、实时跟踪应用程序、在线游戏和协作工具。以下是信任 NodeJs 的全球巨头:*

*LinkedIn、网飞、优步、Trello、PayPal、NASA、易贝、Medium、Groupon、沃尔玛、Mozilla、GoDaddy、Yandex、花旗银行、雅虎*

# *结论*

***Node.js** 确实是个不错的选择。它有一个庞大的社区，它基于 javascript，这是世界上最流行的编程语言。更重要的是，它扩展了 JavaScript 的应用领域，显然可以用于前端和后端服务器。*