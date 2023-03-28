# Swift 异步/等待快速指南

> 原文：<https://medium.com/codex/quick-guide-on-async-await-in-swift-538ba22b20c?source=collection_archive---------1----------------------->

## 了解如何对异步任务使用 async/await。

在 WWDC21 期间，苹果在 Swift 版本中引入了新的 *async/await* 语法，允许我们以更短、更安全的方式编写异步代码。

![](img/a71403c12e5aaa3c782dab91c1ed3a4f.png)

让我们直接跳到一个例子，看看我们创建依赖于两个 API 请求的视图模型的场景。