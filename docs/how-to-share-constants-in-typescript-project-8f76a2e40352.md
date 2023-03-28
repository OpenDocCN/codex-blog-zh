# 如何在 Typescript 项目中共享常数

> 原文：<https://medium.com/codex/how-to-share-constants-in-typescript-project-8f76a2e40352?source=collection_archive---------0----------------------->

## 避免神奇的字符串，使你的应用程序可维护和强类型化

![](img/11b1d80a4614ca58514ae0355836d4d6.png)

在 [Unsplash](https://unsplash.com/s/photos/line?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Ridham Nagralawala](https://unsplash.com/@ridham?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

我们经常需要在类和模块之间共享公共值。这些值可以是错误代码、状态指示器、环境设置、配置值等。最佳实践是将这些值存储在常量中，而不是硬编码的神奇字符串中…