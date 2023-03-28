# JavaScript 是如何征服网络的？第三部分

> 原文：<https://medium.com/codex/how-javascript-conquered-the-web-part-3-da6c9550979b?source=collection_archive---------2----------------------->

![](img/b5b04f4045a805b90590710fd4e36154.png)

来自电子游戏:魔多的阴影

这是 JavaScript 如何征服网络的第三部分，也是最后一部分。这里可以看第一部[，这里](/only-javascript/how-javascript-conquered-the-web-1-6462e5f3d2c0)可以看第二部[。](/only-javascript/how-javascript-conquered-the-web-part-2-249fbae3b364)

> "任何可以用 JavaScript 编写的应用程序，最终都会用 JavaScript 编写."
> — [杰夫·阿特伍德](https://blog.codinghorror.com/the-principle-of-least-power/)，关于 JavaScript 的 Stack Overflow 的联合创始人

![](img/d1a6f00c6fc713f5e353c988ce5ec0df.png)

电影**安娜马莱**中的拉吉尼坎塔

# 2009 年至 2017 年:超越网络

> “尽管 JavaScript 有惊人的缺点，但在本质上，它有一些非常正确的东西。当你揭开盖子，你会发现一种富有表现力和强大的编程语言。这种语言在许多 Ajax 库中被很好地用来管理和扩充 DOM，为以网页形式交付的交互式应用程序提供了一个应用程序平台。Ajax 变得流行是因为 JavaScript 有效。效果出奇的好。”
> —道格拉斯·克洛克福特在文章中“[世界上最被误解的编程语言已经成为世界上最受欢迎的编程语言。](https://www.crockford.com/javascript/popular.html)

## 成熟的 JavaScript 运行时系统

软件开发人员 Ryan Dahl 使用 V8 开发了一个非阻塞的 JavaScript 运行时环境“Nodejs”，并于 2009 年 5 月发布。它还支持文件系统、HTTP 请求、流和子进程。很快，Node 变得像 JavaScript 一样流行，JavaScript 是一种可以在服务器端和客户端使用的单一编程语言。

💡时隔近 9 年，Ryan 发表了关于 Node.js 的 10 件让我后悔的事情的演讲，并发布了另一个运行时间。

## MVC 和单页面 web 应用的时代

jQuery 很好，但对于企业应用程序来说还不够好，因为它是一个旨在帮助 DOM transverses、CSS 选择器和处理浏览器兼容性问题的库。没有提供关于如何正确使用它的结构/模式。如果这个库使用不当，维护一个 jQuery 站点可能会变成一场噩梦。这是使用 MVC 框架模式和 web 向单页面应用程序发展的时候。

谷歌员工 miko he very 正在开发一个附带项目，2009 年晚些时候，这个项目作为开源项目发布，命名为 AngularJS，并成为 MEAN stack 开发的一部分。

2013 年，脸书开发并发布了 [ReactJS](https://reactjs.org/) ，这是一个用于构建用户界面的声明式、高效且灵活的 JavaScript 库。

2014 年，使用 AngularJS 在谷歌工作的开发人员尤雨溪开发并发布了一个轻量级渐进框架 [VueJS](https://vuejs.org/) 。

你可以在这里阅读这三个框架的比较。

[](https://dzone.com/articles/react-vs-angular-vs-vue-the-complete-comparison-to) [## React vs. Angular vs. Vue:完整的比较

### 在这篇文章中，我们讨论的积极和消极的反应，角度，和 Vue 和情况下，每一个是…

dzone.com](https://dzone.com/articles/react-vs-angular-vs-vue-the-complete-comparison-to) 

当时推出的 JavaScripts MVC 框架有 Ember.js、Backbone.js、Knockout.js 等很多，都被广泛使用。

💡一个**单页应用** ( **SPA** )是一个 web 应用或网站，它通过动态重写当前页面而不是从服务器加载整个新页面来与用户交互。该页不会在进程中的任何时刻重新加载，控制也不会转移到另一页。
—维基百科

💡“MEAN stack 是 **M** ongoDB、 **E** xpress.js、 **A** ngularJS(或 Angular)和 **N** ode.js。因为 MEAN stack 的所有组件都支持用 JavaScript 编写的程序，所以 MEAN 应用程序可以用一种语言为服务器端和客户端执行环境编写”
—维基百科

## 移动应用

还为移动开发引入了几个 javascript 框架。其中有[**React Native**](https://facebook.github.io/react-native/)**[**Native script**](https://github.com/NativeScript/NativeScript)**[**Ionic**](https://github.com/ionic-team/ionic)**[**Apache Cordova**](https://github.com/apache/cordova)**和 [**OnSenUI**](https://github.com/OnsenUI/OnsenUI) **。**********

## ****桌面应用程序****

****开发 JavaScript 是为了在 web 上使用它。由于 Node 和 Chrome 浏览器，也可以用它来开发桌面应用程序。****

****[Electron](https://electronjs.org/) 是 GitHub 开发和维护的一个框架。这使得开发人员可以使用 JavaScript、HTML 和 CSS 等 web 技术开发桌面应用程序。****

****[Node web-kit 又名 NWjs](https://nwjs.io/) 是一个非独立的 JavaScript 框架，允许加载本地网站和控制参数，如窗口尺寸、工具栏和菜单项，并提供对本地计算机文件的访问。****

****💡有几个桌面应用程序是使用电子构建的。其中有 [Visual Studio 代码](https://code.visualstudio.com/)、[不和谐](https://discordapp.com/)和 [Windows 95](https://github.com/felixrieseberg/windows95) 。****

## ****JavaScript 和最小功率原理****

****![](img/a1ccf9380344f0a2ca2c01ff97280093.png)****

****大卫 vs 歌利亚****

****当我们为一个项目选择一种方法/编程语言时，我们倾向于寻找一种尽可能强大的语言。但是 JavaScript 没有其他语言强大。但是它存活了很多年，尽管它缺少很多其他语言提供的特性。HTML 是另一个例子。它功能不太强大，但使用起来太简单了。即使在多年以后，它仍然被用于开发 web 应用程序。****

 ****[## 最小功率原理

### 蒂姆·伯纳斯·李论最小权力原则:计算机科学花了 40 年时间创造语言…

blog.codinghorror.com](https://blog.codinghorror.com/the-principle-of-least-power/)**** 

# ****来自 2018:让 JavaScript 无处不在****

> ****“每个人都是强盗，直到一个强盗走进房间”****

****![](img/6b5ccd7d624f717229f827e829b1a5ba.png)****

****黑客帝国重装上阵—(尼奥 Vs 特工史密斯的乘法)****

****💪JavaScript 不仅仅是为 web/桌面开发应用程序。它还可以用于开发视频游戏和物联网等。下面是一些使用 JavaScript 开发的有趣项目。****

*   ****基于网络的视频游戏引擎，如 [Babylonjs](https://www.babylonjs.com) 和 [ImpactJs](https://impactjs.com/)****
*   ****机器人套件，如 [CyclonJs](https://cylonjs.com/) 和[张诗钟五号。](https://github.com/rwaldron/johnny-five)****
*   ****神经网络如 [BrainJs](https://github.com/BrainJS/brain.js)****
*   ****微控制器的解释器，如 [Espruino](https://github.com/espruino) 。****
*   ****自然语言处理器如[妥协](https://github.com/spencermountain/compromise)****
*   ****友好的网络机器学习，如 [ML5](https://github.com/ml5js/ml5-library) 。****
*   ****音乐播放器如 [Webamp](https://github.com/captbaritone/webamp) 和[振幅。JS](https://github.com/521dimensions/amplitudejs)****
*   ****用于网络的增强现实，例如 [AR.js](https://github.com/jeromeetienne/AR.js) 和 [WebXR 设备 API](https://github.com/immersive-web/webxr)****
*   ****IOT 的 JavaScript 引擎，如 [JerryScript](https://github.com/jerryscript-project/jerryscript) 。****