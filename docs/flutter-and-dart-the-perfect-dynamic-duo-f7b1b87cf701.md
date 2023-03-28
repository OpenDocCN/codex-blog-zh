# 颤振和飞镖:完美的动态二重奏

> 原文：<https://medium.com/codex/flutter-and-dart-the-perfect-dynamic-duo-f7b1b87cf701?source=collection_archive---------22----------------------->

## **从头到尾，一部长篇的飞镖史**

W 当 Dart 1.0 首次发布时，Dart 团队对该语言的意图是引入一种可以通过集成不同语言(如 JavaScript、C#、Python 和 Java)的不同功能和工具来轻松学习的语言。但是 Dart 团队想向公众展示的另一个特性是它的跨功能性和对现代网络浏览器的适应性，例如移动和桌面平台。这一宣布令人惊讶，因为已经有一种语言严格用于此目的，这种工具叫做 JavaScript。

Dart 当时的主要能力是，你可以编写原生 Dart 代码，Dart 编译器会把代码编译成原生 JavaScript 代码。传统上，每个 web 浏览器都包含一个可以运行和编译 JavaScript 代码的虚拟机，这实际上取决于每个浏览器的供应商。

安装在几乎每台计算机上的最常见的 web 浏览器之一是 Google Chrome，它使用名为 V8 的虚拟机引擎，有趣的是，该引擎是由 Dart 的创始人 Lars Bak 开发的。

> N 如今，放置一个可以运行本地 Dart 代码的虚拟机引擎的所有意图都消失了，但 Dart 并没有保持不变。

Flutter 是一个用于创建跨平台移动应用程序的流行框架，与 Flutter 一起使用的编程语言主要是 Dart。最近，dart 团队为了市场，推动程序员使用 Flutter 和 dart。然而，这并不意味着 Dart 不再用于 web。从第一天起，许多 Dart 应用程序已经发布，但仍然使用 Dart-to-JavaScript 编译器。

**如今谁在工业中使用它？**

![](img/888142e095301f92b2c216fdb0822ccc.png)

照片由[丹尼尔·伊德里](https://unsplash.com/@ricaros?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Dart 有很多关于您可以用它来构建什么的报道。许多程序员使用框架 Flutter 将 Dart 2.0 用于移动应用程序。其他程序员将 Dart 用于 web 应用程序，并开发 web API 以及帮助他们构建这些应用程序的框架，如 Angular，它现在完全支持 Dart。全栈开发人员将使用 Dart 构建应用程序的 UI，然后将 UI 与同样使用 Dart 构建的后端服务连接起来。最酷的是，您可以在应用程序的整个开发生命周期中使用 Dart。

## 支持范例

从一开始，Dart 就是一种面向对象、函数式和命令式的多范式语言。Dart 支持创建类、抽象类、接口、继承、方法覆盖和多态，使语言成为真正面向对象的语言。

## 基准

与 JavaScript 或 Java 等其他编程语言相比，Dart 的速度可以说是该语言最好的特性之一。因为 Dart 最初的目的是取代 web 浏览器中的 JavaScript，所以基准测试将是 Dart 和 JavaScript 之间的比较。

![](img/d23fc6ab16cb4f7cd31348ee9a77a949.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Aliaksei manlyx](https://unsplash.com/@manlyx?utm_source=medium&utm_medium=referral) 拍摄的照片

根据 Codemagic 的一篇文章，Dart 使用两种编译方法，即 JIT 和 AOT。AOT 代表超前编译。这种编译方法将原生 Dart 代码转换成可理解的机器码。JIT 或 just-in-time 是另一种编译方法，它也将本机代码翻译成机器码。

> H **然而，区别出现在代码执行和部署的时候。**

JIT 是一种有效的方法，可以处理热重载等特性，这种特性通常在使用 Flutter 开发移动应用程序时出现。在热重装开发中，您对代码所做的任何更改都会实时反映到您正在运行的模拟器中。在这种情况下，编译方法是 JIT，它在代码运行时编译代码。当 Dart 本机代码部署到 web 服务器或移动商店(如 Play Store 和 App Store)时，可以看到 AOT 编译方法。编译器方法将代码翻译到负责运行应用程序实例的移动设备芯片上。这个执行发生在使用 AOT 编译代码之后。

## 最终确定

Dart 编程语言本身并不新鲜。然而，由于最近的升级和名为 Flutter 的新框架的发布，Dart 已经成为许多类型的程序员的流行语言。Dart 的起源要追溯到 2012 年，当时这种语言还处于开源状态，只有从 GitHub 克隆项目才能使用。从那以后，Dart 被许多程序员用于不同的项目，包括 web API、前端代码、独立服务器等等。Dart 团队从 Dart 1.0 开始，去年 Dart 2.0 刚刚发布到一个稳定的分支。Dart 2.0 提供了许多其他编程语言所没有的新特性，并且随着 Dart 团队发布的每一个小版本，Dart 2.0 的受欢迎程度都在不断提高。