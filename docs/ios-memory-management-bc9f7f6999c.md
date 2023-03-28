# iOS 内存管理

> 原文：<https://medium.com/codex/ios-memory-management-bc9f7f6999c?source=collection_archive---------3----------------------->

![](img/9f9dfb37b43918eae8b3b56ee0fd5d14.png)

图片由

在 iOS 中，内存管理是分配、使用和取消分配内存的过程，其方式可以最大限度地降低应用程序使用过多内存和被操作系统终止的风险。现在，它支持 ARC，我们不必保留和释放对象。Xcode 会在编译时自动处理这个任务。在本文中，我们将介绍在 iOS 应用程序中管理内存的最佳实践: