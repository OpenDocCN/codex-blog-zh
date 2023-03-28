# AWS 加密(第 2 部分)—客户端

> 原文：<https://medium.com/codex/encryption-with-aws-part-2-client-side-d16367f6a37d?source=collection_archive---------6----------------------->

![](img/82766692b3e3fd1a48f24013d234418d.png)

戈兰·艾沃斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这篇文章是我们讨论加密的系列文章的第二篇，也是最后一篇。

我们在[的第一篇文章](/codex/data-encryption-in-aws-part-1-dca85a0dd19)中回顾了一些加密理论，并介绍了加密在 AWS(服务器端加密)中是如何工作的。在本帖中，我们将讨论客户端加密，这是一种在数据发送到服务器之前在发送方加密数据的技术。那主要是你的应用。这种…