# Python 生成器:简介

> 原文：<https://medium.com/codex/python-generators-an-introduction-6934cd1f5bec?source=collection_archive---------16----------------------->

![](img/08fd6ff82e8df5c7a3fe1e264ef03dc7.png)

由[杰芬·辛普森](https://unsplash.com/@jayphen?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 介绍

在 Python 中，两种语言结构在用户定义的操作中尽可能延迟结果的创建:*生成器函数*和*生成器表达式。*生成器函数编码为普通的`def`语句，但使用`yield`语句一次返回一个结果。生成器表达式类似于 [*列表理解*](/codex/10-python-one-liners-for-lists-da92acf0130a) 但是返回一个按需产生结果的对象，而不是…