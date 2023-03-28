# 基于令牌的安全，OIDC OAUTH，身份服务器—第 4 部分

> 原文：<https://medium.com/codex/tokenbased-security-oauth-oidc-identityserver-part-4-d1c5651ad6c8?source=collection_archive---------3----------------------->

![](img/39c1d5a9078e85ad3b74091d129ce237.png)

约书亚·雷德科普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

在本系列的前一篇[文章](/codex/token-based-security-oauth-oidc-and-identityserver4-part-3-4d8c72d5aed5)中，我们用一些测试用户和客户机配置设置了 IdentityServer。然后，我们使用 postman 工具获得带有几个不同流的访问令牌。

在这篇文章中，我们将继续向前，在我们的解决方案中添加一个**资源 API** 。这将是一个简单的。NET Core WebAPI…