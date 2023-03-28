# SwiftUI 中的属性包装器

> 原文：<https://medium.com/codex/property-wrappers-in-swiftui-a9ee884690e6?source=collection_archive---------1----------------------->

![](img/0a84ab6bb9bbc81b39aac603a010cf91.png)

图片由 [Zan](https://unsplash.com/@zanilic) 在 [Unsplash 上。](https://unsplash.com/)

在您的 SwiftUI 应用程序中，每一个可以改变的数据值或对象都需要一个真实的来源和一个使视图能够改变或观察它的机制。SwiftUI 的属性包装器使您能够声明每个视图如何与可变数据交互。

属性包装将值或对象包装在具有两个属性的结构中: