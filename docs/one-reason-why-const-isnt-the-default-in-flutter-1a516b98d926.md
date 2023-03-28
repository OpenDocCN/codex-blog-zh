# Const 在 Flutter 中不是默认的原因之一

> 原文：<https://medium.com/codex/one-reason-why-const-isnt-the-default-in-flutter-1a516b98d926?source=collection_archive---------4----------------------->

![](img/9a1019b59a4ce411d8bca10b9f38a0a8.png)

由[卢克·迈克尔](https://unsplash.com/@lukemichael?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在 Flutter 2.5 之前，我使用的是 linter 文件。我只是不合理地引以为豪。但是现在有了 Flutter 2.5，每个新的 Flutter 项目都有一个 linter 文件。现在 2.5 中的一些 lints 有点…奇怪。但总比没有 linter 文件好。

但是我想特别谈谈一条棉绒规则。这条规则告诉你使用`const`关键字…