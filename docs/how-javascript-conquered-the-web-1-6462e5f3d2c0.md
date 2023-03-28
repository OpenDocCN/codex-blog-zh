# JavaScript 是如何征服网络的？第一部分

> 原文：<https://medium.com/codex/how-javascript-conquered-the-web-1-6462e5f3d2c0?source=collection_archive---------0----------------------->

![](img/1a416bb69bb1727de82abd43d99b594d.png)

巴德对史矛革。霍比特人:五军之战

> “幸运不如聪明” *—道格拉斯·克洛克福特关于* [*JavaScript*](https://www.crockford.com/javascript/popular.html)

20 年前，如果有人说 JavaScript 将在未来统治互联网，那么没有人会相信。因为没有人会这样想，JavaScript 也不是为此而设计的。这种语言仅用了 10 天就被创造出来，尽管有很多好的部分，但它的缺陷还是受到了很多批评。

尽管它是最令人讨厌的编程语言之一，但它存活了多年，并成为最流行的编程语言之一、最常用的编程语言和事实上的 web 标准编程语言。

# JavaScript 是什么？

JavaScript 通常缩写为 JS，是一种高级、实时编译、面向对象的编程语言，符合 ECMAScript 规范—维基百科

## 浏览器大战

20 世纪 90 年代初，互联网仍在发展，并没有被很多人使用。那时，网络浏览器没有和操作系统一起发布，人们必须单独购买。一家美国计算机服务公司 Netscape 通信公司开始免费分发他们的浏览器“Netscape Navigator ”,并对商业用途收费。很快，它成为使用最多的浏览器，占据了近 80%的浏览器市场份额。

微软在浏览器竞赛中落后了。1994 年末，微软授权了早期商业网络浏览器 Spyglass Mosaic，并开发了自己的浏览器 Internet Explorer。他们开始免费发布 Internet explorer 和他们的 Windows 操作系统，很快他们就控制了市场份额。

![](img/8794d8c9133a701b3e40a1074dc6937e.png)

来自一部古老的印度电影

💡最终，当谷歌推出自己的浏览器 Chrome 时，微软在浏览器大战中输给了谷歌。

# 1995 年至 1999 年:开始

网景公司想在他们的浏览器中嵌入编程语言方案，因为他们认为网络需要一种能与 DOM 交互的小型脚本语言。因此，动画和互动将成为未来网络的一部分，网络将变得更加动态。

Java 的创造者 Sun Microsystems 希望 Java 也能与浏览器一起工作。但是 Java 没有 HTML 那么小。太大了，放不进浏览器。Netscape 和 Sun 合作，决定开发一种脚本语言，而不是嵌入方案。我们的想法是开发一种不那么复杂的语言，让程序员甚至非程序员可以直接在网页上编写和 HTML 一样的代码。

上面 YouTube 视频中的家伙 Brenden Eich 于 1995 年 5 月被招募，他在短短 10 天内开发了名为 Mocha 的 JavaScript。它仅仅是一个编程语言的原型。语法类似于 Java。它还具有类似 Scheme 的一级函数、与 LISP 相同的动态类型等特性。

JavaScript 与网景浏览器整合，然后在 1995 年 9 月更名为“LiveScript”。尽管 LiveScript 这个名字很好，但对这些公司来说还不够好。那时，Java 开始受到关注，两家公司都希望新的脚本语言与 Java 相似。因此，他们可以将 Java 作为企业系统来推广，并将 JavaScript 用于 web。1995 年 12 月，作为一种营销策略，它被重新命名为 JavaScript。

 [## 新闻稿

### 加利福尼亚州山景城(1995 年 12 月 4 日)-网景通信公司(纳斯达克股票代码:NSCP)和太阳微系统公司…

web.archive.org](https://web.archive.org/web/20070916144913/http://wp.netscape.com/newsref/pr/newsrelease67.html) 

## JavaScript vs ECMAScript。

初学者的一个困惑是区分 JavaScript 和 ECMAScript。我们知道 JavaScript 是一种脚本语言。但是 ECMAScript 是什么呢？

![](img/03a059ef4f9c3305a7259f8071ad3a1f.png)

一个蹒跚学步的孩子被双胞胎爸爸弄糊涂了

1997 年，网景公司想标准化 JavaScript 并提交给 ECMA。在标准化过程中，ECMA 提出了新的标准 ECMA-262。由于商标问题，新标准不能被命名为 JavaScript。所以它被简单地命名为 [ECMAScript](http://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262,%201st%20edition,%20June%201997.pdf) ，任何脚本语言如 ActionScript、JScript 都应该遵循新标准。

在 20 世纪 90 年代后期，ECMAScript 中不断添加/更新功能，然后被 JavaScript 采用。ECMAScript 2 于 1998 年中期发布，主要是解决 ECMA 和 JavaScript ISO 标准之间的不一致。

💡欧洲计算机制造商协会(ECMA)是一个会员制的信息和通信系统组织。它的成立是为了使欧洲的计算机系统标准化，它积极地为信息技术和电信领域的全球标准化做出了贡献。

# 2000 年至 2005 年:衰落

> “改变一开始很难，中间很乱，最后很华丽。”
> *—罗宾·夏尔马*

1999 年底， [ECMAScript 3](http://www.ecma-international.org/publications/files/ECMA-ST-ARCH/ECMA-262,%203rd%20edition,%20December%201999.pdf) 发布，主要变化包括使用 try/catch 块进行异常处理。ECMAScript 3 是一个广泛使用的版本，当时所有的主流浏览器都支持它，并且持续支持了很多年。

## **JScript:从来不需要的脚本语言**

当微软知道 JavaScript 时，该公司立即开始对其进行逆向工程，并于 1996 年开发了自己的脚本语言 JScript。尽管它被称为 JScript 是为了避免商标问题，但 JScript 和 JavaScript 只是或多或少有些相同。但是，也有不同之处，比如条件编译。

微软赢得了第一场浏览器大战，并占据了大量市场份额。所以人们被迫使用微软的 ECMAScript 实现，也就是 JScript，而这些实现[偏离了 ES-3 规范](https://regmedia.co.uk/2007/10/31/jscriptdeviationsfromes3.pdf)。开发人员感到非常沮丧，因为他们必须为多种浏览器分别编码。

💡微软为 visual basic 开发人员又发布了一种脚本语言。它叫做 VBScript。

## **跨浏览器兼容性问题**

![](img/f48de8008317dc57a21fc27499ea694a.png)

黑客帝国重装上阵—(尼奥 Vs 特工史密斯的乘法)

几年之内，互联网的使用持续增长。在 20 世纪 90 年代，Netscape Navigator 和 Internet Explorer 是使用最广泛的浏览器。公司意识到浏览器是更好的网络交流方式。因此，他们开始引入自己的浏览器，而 ECMAScript 的实现在每个浏览器中都有所不同，这就产生了许多兼容性问题。

1.  一个测试软件，Opera 于 1995 年中期发布。后来它变成了免费软件。
2.  2002 年底， [Firefox](https://www.mozilla.org/en-US/firefox/new/) ，一款基于 Netscape Navigator 代码库的浏览器发布。
3.  苹果公司的 Safari 于 2003 年初发布，用于苹果产品。
4.  2008 年底，谷歌发布了自己的浏览器 [Chrome](https://www.google.com/chrome/) 。

💡又有一款浏览器 [brave](https://github.com/brave/brave-browser) 最近由 brave 软件公司[发布，Brenden Eich 是该公司的首席执行官。](https://brave.com/)

## **基于网络的多媒体平台**

网络最具挑战性的部分之一是引入动画和互动内容。这是由于缓慢的互联网连接和浏览器的无能。

1995 年，FutureWave Software 发布了 SmartSketch，这是一款适用于 [pen](https://en.wikipedia.org/wiki/Pen_computing) 电脑的矢量绘图应用，后来被修改为基于矢量的网络动画工具，并被命名为 FutureSplash Animator。1996 年，Macromedia 收购了 FutureWave，并将该工具更名为 Macromedia Flash。与此同时，Sun Microsystems 推出了 Java applet，这是一个可以从网页启动并在单独的进程中执行的小应用程序。

在 21 世纪初，Flash 和 Java applet 都被大量用于开发丰富的 web 应用程序。两者都在所有浏览器和操作系统中运行良好，无需任何特殊代码。微软也开发了一个类似 Flash 的框架 Silverlight。

用 Flash 开发的简笔画格斗游戏

💡在后来的几年里，Flash，Silverlight 和 Java applet 被 HTML5 所主导，现在这些都过时了。

💡史蒂夫·乔布斯严厉批评 Flash，并在 2010 年年中停止在苹果设备中支持 Flash。

 [## 关于 Flash - Apple 的思考

### 苹果与 Adobe 的关系由来已久。事实上，当 Adobe 的创始人在他们众所周知的车库里时，我们见过他们…

www.apple.com](https://www.apple.com/hotnews/thoughts-on-flash/) 

你可以在这里阅读第二部。