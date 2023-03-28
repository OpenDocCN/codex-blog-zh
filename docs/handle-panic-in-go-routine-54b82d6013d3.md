# 处理 go 例程中的恐慌

> 原文：<https://medium.com/codex/handle-panic-in-go-routine-54b82d6013d3?source=collection_archive---------5----------------------->

![](img/45b93f12bbb5c74f3949ab4959d6c502.png)

**简介:**golang 开发者普遍认为该语言提供了一个名为`panic()`的函数来抛出致命错误。生产就绪的 Go 应用程序肯定应该使用内置的`recover()`函数来处理这种混乱，否则应用程序将抛出混乱并退出。因此，如果没有`recover()`功能，一个典型的 Go 应用程序将从恐慌场景中退出，我们将无法访问该应用程序，除非我们…