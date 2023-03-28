# 有效的 Java:明智地使用惰性初始化

> 原文：<https://medium.com/codex/effective-java-use-lazy-initialization-judiciously-96cfa1dc288d?source=collection_archive---------1----------------------->

![](img/9d1def1f5fbf3a4853b2cdde684a83e6.png)

阿德里安·斯旺卡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

*惰性初始化*是一种将对象或进程的创建推迟到需要时才进行的模式。这种模式背后的思想是，您可能永远不需要该对象，因此您将节省初始化成本。使用惰性初始化的主要原因是为了优化。惰性初始化的另一个用途是打破代码中棘手的循环依赖。