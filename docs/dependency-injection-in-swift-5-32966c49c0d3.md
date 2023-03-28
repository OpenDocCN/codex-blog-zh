# Swift 5 中的依赖注入

> 原文：<https://medium.com/codex/dependency-injection-in-swift-5-32966c49c0d3?source=collection_archive---------4----------------------->

![](img/736a754738f9ffe4facfa869436ab74b.png)

图片由 [Unsplash 的](https://unsplash.com) [James Yarema](https://unsplash.com/@jamesyarema) 拍摄。

iOS 开发人员已经使用依赖注入(DI)很多年了。这是一种在 iOS 应用中分离组件的技术，通过注入它们所需的服务，而不是硬编码这些依赖关系。这允许更多模块化的、可测试的代码。

Swift 5 引入了新的属性包装器，使得 DI 更加容易。属性包装器是向属性添加功能的代码片段，不需要…