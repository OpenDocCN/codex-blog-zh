# 震惊！发现 7 个非常有效的 JavaScript 技巧。

> 原文：<https://medium.com/codex/shock-discover-7-javascript-tricks-that-work-so-well-a618808e7878?source=collection_archive---------2----------------------->

![](img/21196eea453136b8a05ae5483046dac9.png)

来自 Stockphotos.com 的阿莫斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## **1。把一个字符串分割成一个字符数组，你会怎么做？**

对于大多数工程师来说，将字符串拆分成字符组的方法太多了。在这里，我将使用扩展运算符(…)来解释我更喜欢什么:

```
const word = 'risk'const characters = [...word]
```