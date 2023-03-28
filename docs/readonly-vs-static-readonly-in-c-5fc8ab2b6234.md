# C#中的只读与静态只读

> 原文：<https://medium.com/codex/readonly-vs-static-readonly-in-c-5fc8ab2b6234?source=collection_archive---------7----------------------->

本文将探讨静态和只读 C#字段之间的区别。基本上，静态只读字段永远不能被引用类型的实例替换。相反，const 常量对象不能被新对象替换。您将了解区别以及如何对只读字段使用内联初始化。最终，本文将帮助您编写更好的代码。

![](img/622e55e130459f327f8e79b57a2ee450.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Maksym Zakharyak](https://unsplash.com/@zakharyak?utm_source=medium&utm_medium=referral) 拍摄的照片