# 如何创建 FastAPI/uvicon 服务器 Windows 服务

> 原文：<https://medium.com/codex/how-to-create-a-fastapi-uvicorn-server-windows-service-af41f075dabf?source=collection_archive---------0----------------------->

![](img/787c6a990bf0c1ecb23186ffeccbb791.png)

照片由[伊内斯·皮门特尔](https://unsplash.com/@deadqueenines?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

最近，我发现自己想要创建一个 Windows 服务，负责在启动时自动启动 FastAPI 服务器。然而，我找不到任何文章或教程以结构化和详细的方式涵盖它。因为我得到了它的工作，所以想与社区分享它，这样如果其他人想做同样的事情，它会更容易。