# 点网络库与核心和框架的兼容性

> 原文：<https://medium.com/codex/dot-net-library-compatibility-with-core-and-framework-6c8e29cc80e0?source=collection_archive---------6----------------------->

*兼容. Net 框架历史版本的挑战*

我非常感激[。NET 生态系统现在正常开源](https://dotnet.microsoft.com/en-us/platform/open-source)。在推出 DotNetCore 之前，我们使用的是现在被称为[的 DotNet 框架](https://en.wikipedia.org/wiki/.NET_Framework)——现在仍然有很多 DotNet 框架软件在外面。

对于我们这些在 NuGet 上发布软件的人来说，花些时间让我们的软件与. Net 框架兼容是值得的。让我们研究一下如何建立一个兼容的库…