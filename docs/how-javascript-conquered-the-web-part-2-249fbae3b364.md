# JavaScript 是如何征服网络的？第二部分

> 原文：<https://medium.com/codex/how-javascript-conquered-the-web-part-2-249fbae3b364?source=collection_archive---------1----------------------->

![](img/2a8b94d683e629de308f4be007745fca.png)

《权力的游戏》中的一个场景

这是《T2》关于 JavaScript 如何征服网络的第二部分，你可以在这里阅读第一部分。

> “改变一开始很难，中间很乱，最后很华丽。”
> *—罗宾·夏尔马*

# ECMAScript 4:勉强推向企业应用

JavaScript 是作为一种轻量级编程语言开发的，它并不意味着大规模的应用程序。当时，Macromedia Flash 中使用了另一种基于 ECMAScript 的脚本语言 ActionScript。Flash 是网络上的热门话题，因为它在许多网站上被用来制作交互式动画、图形。因此，一些人认为 ECMAScript 应该面向更多的受众，他们开始提出一些变化范围很大的特性。

发现了数据交换格式 JSON 的道格拉斯·克洛克福特，也是广受好评的《Javascript:好的部分》一书的作者，提出了他对 ECMAScript 4 提案的关注。他觉得这些提议太复杂了，微软团队同意了，因为他们正在把 JScript.NET 推向网络，而 Internet Explorer 占据了几乎 90%的浏览器市场份额。所以他们想控制局面。

讨论和提议持续了数年，但没有任何成果，最终 ECMAScript-4 在 2008 年被废弃。

💡JScript。NET 是微软的一部分。Net 框架。这个想法是使用 ASP.NET 语作为服务器端语言，JScript.NET 语作为客户端语言。

# 2005 年至 2009 年:AJAX 的崛起

早在 1999 年末，微软为 Microsoft Exchange Server 2000 开发了一个小的接口/功能 *IXMLHTTPRequest，并将其添加到 Internet Explorer 5 中。这有助于从其他网页获取数据。尽管它不是一个标准，但它被用于 Gmail、谷歌地图等应用中。在 2000 年代中期，它被添加为 web 标准。*

💡AJAX(异步 JavaScript 和 XML)这个术语是 Jesse James Garrett 在 2005 年首次提到的，你可以在这里阅读。

## jQuery:浏览器兼容性问题的救星

约翰·雷西格，一个大学生，对浏览器兼容性问题感到沮丧，他开发了一个名为 [jQuery](https://jquery.com/) 的 JavaScript 库以及非常好的文档。这个项目获得了巨大的成功，人们开始意识到 JavaScript 能做什么。在 T4 1000 万个最受欢迎的网站中，超过 70%的网站都在使用它。

![](img/9c1c3c74eb3d09bc9977d54773c8392b.png)

来自一部古老的印度电影

在那个时候，有许多库/框架，如 prototype.js，Dojo 被构建和发布。

💡也可以像 jQuery 一样开发自己的库。

[](https://github.com/nefe/You-Dont-Need-jQuery/blob/master/README.md) [## nefe/你不需要 jQuery

### 如何做查询，样式，dom，ajax，事件等的例子，如普通 javascript 的 jQuery。— nefe/You-Dont-Need-jQuery

github.com](https://github.com/nefe/You-Dont-Need-jQuery/blob/master/README.md) 

## JSON:一个完全重新发明的轮子

十多年来，XML 被用于通过网络在不同系统之间交换数据。但是它有自己的局限性，例如难以理解，解析文档时速度慢，文档有时很大，并且有多余的信息，例如结束标记。

在 2001 年中期，道格拉斯·克洛克福特和他的团队试图在他们的应用程序中共享页面加载后的数据，这对于 Internet Explorer 通过 XMLHttpRequest 是可能的。但是 XMLHttpRequest 不被网景等其他浏览器支持。所以他们决定使用一个在两种浏览器中都有效的 JavaScript 对象。人们开始用它来分享数据。因此，克罗克福德提出了 JSON 的规范，并将其添加到 Json.org 网站上。经过几次迭代，这成为 2013 年 ECMA 标准之一，并被命名为 JavaScript Object Notation。

💡当从事 XML 开发的开发人员 Dave Winer 感到沮丧并写了一篇博客，认为不需要 JSON 作为其重新发明的轮子时，Crockford 评论说，“[重新发明轮子的好处是，你可以得到第一轮](https://scripting.wordpress.com/2006/12/20/scripting-news-for-12202006/)。”

💡Crockford 没有避免使用保留字，比如 ***do*** ，而是强制要求所有 JSON 键都必须加引号。JavaScript 解释器会将带引号的键视为字符串，并且可以安全地使用保留字。这也是 JSON 键被引用至今的原因。

## 谷歌的 Chrome 浏览器

谷歌一直是技术的领导者，他们的产品如网络搜索、Gmail、谷歌地图在用户体验方面都非常好。为了带来更好的用户体验，他们大量使用了 XMLHttpRequest、sockets 和 prefetch 等特性。但是他们不得不依赖像 internet explorer 这样缺乏标准的浏览器，而且谷歌不得不付钱给他们，让他们把谷歌作为浏览器的默认搜索引擎。这导致谷歌开发了自己的浏览器。

2008 年 9 月，谷歌发布了带有 JavaScript 引擎 V8 的 Chrome，该引擎旨在提高网页浏览器内 JavaScript 执行的性能。

![](img/4157774b21fd4be5a18846382c0215dd.png)

托尼·斯塔克首次出演《绿巨人》

💡与浏览器一起，谷歌发布了一本解释浏览器内部工作原理的漫画书。这是由美国著名漫画家斯科特·麦克克劳德创作的。

[](https://www.google.com/googlebooks/chrome/big_00.html) [## 谷歌浏览器

### 看看谷歌 Chrome 的引擎盖下，斯科特对关键工程决策的漫画解释…

www.google.com](https://www.google.com/googlebooks/chrome/big_00.html) 

## V8 (JavaScript 引擎)

在发布 Chrome 浏览器的时候，谷歌发布了一个用 C++编写的名为 V8 的开源 JavaScript 引擎。V8 的目的是编译和执行 JS 代码。引入了几个 JavaScript 引擎，其中一些是，

*   [**蜘蛛猴**](https://en.wikipedia.org/wiki/SpiderMonkey_(JavaScript_engine))——为火狐。
*   [**查克拉** (JavaScript)](https://github.com/Microsoft/ChakraCore) —针对微软 Edge。
*   [**JerryScript**](https://en.wikipedia.org/wiki/JerryScript)—面向物联网(IOT)。

JavaScript 是作为可用于小程序的脚本语言开发的。但是一旦人们开始使用 Chrome，事情就开始朝着有利于 JavaScript 的方向发展。

![](img/aad2e5dc19e434cd7b2ef8066dcbf8f0.png)

好吧，孩子，你现在是复仇者了。来自《复仇者联盟:无限战争》

这里可以看 part-3 [。](/only-javascript/how-javascript-conquered-the-web-part-3-da6c9550979b)