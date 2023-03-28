# 从 API 中分离测试

> 原文：<https://medium.com/codex/decoupling-tests-from-the-implementation-interfaces-5842380f9656?source=collection_archive---------6----------------------->

## 更改接口(例如 API web 处理程序、队列事件处理程序、网页)是很常见的，并且会影响许多测试。我们如何让他们与这些变化脱钩？

![](img/3f3e86b2931c5e551af1808bc340fb95.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Bernd Dittrich](https://unsplash.com/@hdbernd?utm_source=medium&utm_medium=referral) 拍摄的照片

理想情况下，重构时，我们不想更新任何测试。除了重构的痛苦之外，想想进行 HTTP 调用或求助于…