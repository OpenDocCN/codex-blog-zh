# 关于 API 和 REST 架构(第 1 部分)

> 原文：<https://medium.com/codex/on-apis-and-rest-architecture-part-1-f64449a2f177?source=collection_archive---------6----------------------->

![](img/bb6161d66cb419a78504d653938e46f4.png)

鲁特维克·库尔卡尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## JavaScript 中客户端-服务器交互的介绍

你好！今天，我将介绍客户端-服务器交互，以及我们如何使用 API 与第三方软件进行交互，包括在使用 JavaScript 编写代码时如何实现我们自己的 API。这个主题内容丰富，为了简洁起见，我不会深入任何一个概念。这篇文章是关于这个主题的摘要，不适合那些寻求深入学习服务器端交互基础的人。我建议您对 API 有一个基本的了解，以及如何在 web 应用程序中使用 API 来利用客户端软件和遵循 HTTP 协议的服务器端软件之间的交互，包括 REST 架构的基础知识以及是什么使 web API RESTful。但是，如果您想更深入地了解这个主题，我在本文末尾的参考书目中提供了各种资源的链接。说到这里，让我们开始吧！

# HTTP 和 API

当从客户端(浏览器)与服务器通信时，两者之间会进行交换，包括使用超文本传输协议(HTTP)的数据请求和包含所请求数据的响应。使用资源路径的字符串表示，或*统一资源标识符* (URI)，客户端向服务器发送资源请求，指定诸如凭证和所请求资源的位置之类的值。服务器依次验证该请求，并以包含 HTTP 状态码的响应的形式提供对 HTTP 请求的响应，该 HTTP 状态码指示请求的状态(是被接受、被拒绝、未找到等等)。)，以及所请求的资源。

## HTTP 请求

HTTP 报头包含对提供所请求的资源或服务的服务器有用的信息(即，内容类型和长度、授权令牌、验证凭证、要获取的资源的位置等)。).请求体是 HTTP 请求的一个可选部分，它包含关于请求获取资源的附加信息，该请求使用内置方法来操作一个名为*动词、*的资源，例如 GET、POST、PUT 或 DELETE。这些 HTTP 动词是一组操作的一部分，这些操作主要用于从服务器创建、读取、更新和删除数据，称为 *CRUD 操作*。这些操作由 HTTP 分别以 *post()* 、 *get()* 、 *push()* 和 *delete()* 的形式实现。

## HTTP 响应

服务器一旦接收到 HTTP 请求，就向客户机发送一个响应，该响应也包含一个报头，有时还包含一个主体。响应标头包含一个状态行，其中包含 HTTP 版本、关于响应本身的状态代码(成功、重定向、客户端错误、服务器错误等)。)，以及发送给客户端的关于响应状态的消息。HTTP 响应头还可能提供任何不适合响应状态行的附加信息。HTTP 响应的最后一部分是响应体，可以定义(即内容类型和内容长度)或编码。

## Web APIs

API(应用程序接口)是与第三方软件(在 web APIs 的情况下，客户端和服务器)交互的抽象。Web APIs 是一种契约，它定义了来自所述软件的公开方法，这些方法用于利用它们的*接口*的使用，这些对象允许与否则无法访问的资源进行交互。API 提供的功能范围从跨浏览器兼容性到 CRUD 操作等等。鉴于 web 上存在如此多不同的软件实现，API 提供了一种一致的方式来请求客户端可以使用的任何第三方软件。例如，一个更常见的 API 是 Fetch API，它公开了一个用于发出请求的接口。下面的示例演示了如何使用 Fetch API 编写请求:

使用 fetch()查询资源并返回包含响应的承诺链(创建于 [https://carbon.now.sh)](https://carbon.now.sh))

Fetch API 为异步获取第三方资源提供了一个全局 *fetch()* 方法。在上面的简单示例中，fetch()方法没有接收到所需资源的路径字符串，而是返回一个包含响应的承诺，以及使用接收到的数据运行的指令(上面的示例记录了它)。

## 表征状态转移(REST)

大多数 API 都提供内置功能，这些功能被实现为本地托管或动态访问。特别是本地托管的 API 使用本地托管的方法，利用一组称为*表述性状态转移*或 REST 的架构指南来利用客户端-服务器交互。这种实现的抽象由一组 API 应该强调的功能组成，包括:

*   组件间交互的可伸缩性
*   组件的独立部署
*   允许跨浏览器兼容性的统一界面
*   创建分层体系结构以促进缓存组件，从而减少用户感知的延迟并加强安全性
*   封装遗留系统

除了这些准则，REST 还定义了与服务器交互时必须满足的六个约束:

*   *客户机/服务器交互*:将创建一个 RESTful API，关注服务器和客户机之间的交互。任务必须在服务资源的提供者和请求者之间进行划分。例如，我们可以使用 HTTP 从客户端向服务器发送一个请求，使用 HTTP 动词来指示请求的类型，服务器将依次提供适当的响应。
*   *无状态表示*:服务器不会存储任何请求数据。所有数据都将存储在客户端中(即会话令牌)。
*   *缓存*:RESTful API 将在客户端和服务器之间提供共享缓存。
*   统一接口(Uniform Interface): RESTful API 将提供操作数据的操作，通常是 JSON 或 XML 格式的。
*   *分层系统*:对 RESTful API 的访问将通过 HTTP 请求头属性中提供的验证来利用。header 对象将包含键/值对，为服务器提供有用的信息，例如身份验证令牌、API 键、内容类型和长度、HTTP 动词(Get、Post、Put、Delete)等。
*   *按需编码*:在客户端以内置方法和保留关键字的形式发送任何 HTTP 请求之前，服务器将向客户端提供可扩展的功能。

一些比较著名的 API 包括 Fetch API、filesystem API 等。尽管向客户机公开了不同的功能，REST 还是推荐这些准则来优化客户机和服务器之间的交互。

伙计们，现在就到这里吧！在本系列的下一篇文章中，我将讨论 CRUD 操作及其在客户端-服务器交互中的作用。下次见！

## 引用的消息来源

> *FreeCodeCamp.org。*“面向初学者的 API——如何使用 API(完整课程/教程)。” *YouTube，YouTube，2019 年 12 月 17 日，*[*https://www.youtube.com/watch?v=GZvSYJDk-us.*](https://www.youtube.com/watch?v=GZvSYJDk-us.)
> 
> *维基贡献者，*《具象状态转移》，*维基，自由百科，*[*https://en.wikipedia.org/w/index.php?title = presentation _ state _ transfer&oldid = 1030195274*](https://en.wikipedia.org/w/index.php?title=Representational_state_transfer&oldid=1030195274)*(2021 年 6 月 27 日访问)。*
> 
> *Mozilla 开发者网络。*“HTTP | MDN。”MDN 网络文档”，*[*https://developer.mozilla.org/en-US/docs/Web/HTTP.*](https://developer.mozilla.org/en-US/docs/Web/HTTP.)*2021 年 6 月 27 日访问。**
> 
> *"典型的 HTTP 会话— HTTP | MDN . " *MDN 网络文档，*[](https://developer.mozilla.org/en-US/docs/Web/HTTP/Session.)**2021 年 6 月 29 日访问。***
> 
> ***维基媒体项目的贡献者。*“API——维基百科。”*维基百科，自由百科全书，维基媒体基金会公司，2001 年 7 月 30 日，*[*https://en.wikipedia.org/wiki/API.*](https://en.wikipedia.org/wiki/API.)**
> 
> ***维基媒体项目的贡献者。"*界面(计算)—维基百科。"*维基百科，自由百科全书，维基媒体基金会公司，2004 年 4 月 26 日，*[*https://en . Wikipedia . org/wiki/Interface _(计算)#Software_interfaces。*](https://en.wikipedia.org/wiki/Interface_(computing)#Software_interfaces.)**