# 10 个最常见的 HTTP 响应状态代码的简短清单

> 原文：<https://medium.com/codex/a-short-cheatsheet-of-the-10-most-common-http-response-status-code-91c5f83f9af3?source=collection_archive---------10----------------------->

## 作为一名开发人员，这个简短的列表可以让你更有效率并节省时间

![](img/f24ef7a1f4b12fe7a60970683c9646a5.png)

有一天，我在工作中创建了一个新的合并请求，我意识到一件事:我不知道 HTTP 响应 200 和 201 之间的区别，也不知道 204 的存在。总结一下:**我对 HTTP 响应一无所知。**

然而，我是一名后端开发人员，了解这类事情**是业内的基本**。因为这个原因，我开始了解它。我练习了一会儿，就精通了这门学科。

但是几天过去了，我还是有些怀疑**。**没错，我完全明白自己在做什么，但是**我把 200 和 201** 搞混了，我已经忘了 401 是什么。所以我创建了一个**备忘单**。

我将与你分享一张**简短的备忘单**，它是我在工作中发现的**最常见的 HTTP 响应**的汇编。

这个列表的目的是**解决你在处理 HTTP 响应**时可能有的疑问。****

当你处理 HTTP 响应时，你准备好停止怀疑了吗？

# 10 个最常见的 HTTP 响应备忘单

根据我自己作为后端开发人员的工作经验，最常见的 HTTP 响应是:

*   200 —好的。请求成功。
*   **201 —已创建**。请求成功，新的资源被创建。
*   **202 —已接受**。请求已被接受进行处理，但处理尚未完成。这在异步操作中很常见。
*   **204 —无内容**。没有内容可作为对此请求的答复发送。例如，它可以用在删除操作中。
*   **400 —错误的请求**。由于客户端错误，服务器无法处理该请求。
*   **401 —未授权**(未认证)。客户端必须通过身份验证才能获得响应。
*   **404 —未找到**。服务器在服务器上没有找到任何与请求 URL 匹配的内容
*   **500 —内部服务器错误**。服务器不知道如何处理这种情况。
*   **502 —坏网关**。当服务器作为网关工作时，它得到无效的响应。
*   **503 —服务不可用**。服务器未准备好处理请求。例如，当服务器过载时就会出现这种情况。

如果您阅读本文是因为您是一名 web 开发人员或者您使用 REST APIs，那么您必须看看这些文章:

[](https://javascript.plainenglish.io/clean-codes-3-most-useful-practices-using-javascript-examples-e80721ad3d4) [## 使用 JavaScript 示例清理代码的 3 个最有用的实践

### 提高代码质量的 3 个实践:正确命名变量，保持参数数量最少

javascript.plainenglish.io](https://javascript.plainenglish.io/clean-codes-3-most-useful-practices-using-javascript-examples-e80721ad3d4) [](https://javascript.plainenglish.io/discover-jest-in-3-minutes-a-simple-and-amazing-javascript-testing-framework-11abdc04a9c3) [## 5 分钟发现 Jest:一个简单而神奇的 JavaScript 测试框架

### 你想成为更好的 JavaScript 开发者吗？然后你必须了解测试，你必须了解…

javascript.plainenglish.io](https://javascript.plainenglish.io/discover-jest-in-3-minutes-a-simple-and-amazing-javascript-testing-framework-11abdc04a9c3) 

但是，如果您只想了解 HTTP 响应的更多信息，这里有一些额外的资源。

# 资源

在顶部，你只有从我的角度和我的日常工作中最常见的 HTTP 响应。尽管如此，也许您需要使用不在列表中的 HTTP 响应，所以这里有一个 T2 最佳资源的简要列表，当我想了解更多关于 HTTP 响应的信息时，我会使用它:

*   [Mozilla 开发者](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)。
*   [维基百科列表](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)。
*   [Umbraco](https://umbraco.com/knowledge-base/http-status-codes/) 。(如果你不需要解释，只想要数字和意义，这个网站就是纯魔术)。

![](img/0662dc009093c2cf26555a66219b8e13.png)

[图像](https://www.pexels.com/es-es/foto/mano-de-la-persona-en-la-computadora-portatil-461064/)来自[像素](https://www.pexels.com/es-es/foto/mano-de-la-persona-en-la-computadora-portatil-461064/)

# 最后的想法

一方面，我们是互联网用户。我们每天都在许多网站上冲浪，作为这次导航的结果，我们收到了 HTTP 响应。我从自己的经历中知道，最令人沮丧的事情莫过于收到 HTTP 响应却不知道其含义的 T21。你不知道这些数字意味着什么，因此，你不能做任何事情来解决这个问题。

另一方面，如果你正在阅读我的文章，你可能是一名开发者，一名网络开发者。我们必须每天使用 REST APIs。因此，了解 HTTP 响应在我们的领域是必须的。然而，有许多回应，每一个都有一个不同的含义。对他们中的任何一个有怀疑或困惑都是正常的。

出于这个原因，我创建了一个简短的备忘单，包括我在日常工作中发现的最常见的 HTTP 响应。我想它可能对你也有用，所以我决定分享这篇文章。

你在工作中使用 HTTP 响应吗？你从来不记得的 HTTP 响应是什么？

# 结论👋

谢谢大家！非常感谢您阅读这篇文章。如果您想了解更多关于技术和发展的信息，请不要忘记**关注我**。我很想知道你对此的看法，所以不要花花公子**写在评论里**，我会读给你听。

如果这篇文章帮助你记住了，你可以**为它鼓掌**并与你的战友分享。

# 关于作者🤓

嗨！很高兴见到你！我是**赫苏斯·拉加雷斯**。目前，我是一名后端软件工程师，正在加的斯大学完成我的计算机科学学位。

我的两大爱好是**技术**和**交流**，所以我会抓住一切机会谈论或撰写关于技术的文章。我喜欢把复杂的概念转换成每个人都能理解的简单概念。

# 想要连接吗？📲

📸[**Instagram**](https://instagram.com/jesuslagares_)**|**💼 [**领英**](https://www.linkedin.com/in/jesus-lagares/) **|** 📹[Youtube](https://www.youtube.com/c/Jes%C3%BAsLagares)**|**🐦 [**推特**](https://twitter.com/jesuslagares_)

📩**jesuslagaresgalan@gmail.com**

谢谢！❣️