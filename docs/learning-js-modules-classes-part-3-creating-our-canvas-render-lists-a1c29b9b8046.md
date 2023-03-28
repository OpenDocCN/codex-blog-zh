# 学习 JS 模块和类第 3 部分:创建我们的画布渲染列表

> 原文：<https://medium.com/codex/learning-js-modules-classes-part-3-creating-our-canvas-render-lists-a1c29b9b8046?source=collection_archive---------7----------------------->

[继续上次创建的特性检测和错误处理器](/codex/learning-js-classes-and-modules-part-2-feature-detection-8e11b645bcb0)，现在是时候开始使用 HTML 5 的画布绘制东西了。我使用 canvas 做任何事情已经有一段时间了，但是我对它的了解远远超过了对 ECMAScript 6 特性的了解。

因此，制作一个模块来创建和缩放我们的画布是相当容易的。缩放它很重要，因为 CSS3 的“纵横比”支持不稳定，并且只在一个轴上工作。我们希望添加一个 window.resize 处理程序，它将保持一个