# 基于令牌的安全，OIDC OAUTH，身份服务器—第 5 部分

> 原文：<https://medium.com/codex/tokenbased-security-oauth-oidc-identityserver-part-5-134f7a9f4a13?source=collection_archive---------4----------------------->

![](img/eff639790d88faead6b0eb580c384f96.png)

照片由 [Fotis Fotopoulos](https://unsplash.com/@ffstop?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 介绍

在本系列的前一篇[文章](/codex/tokenbased-security-oauth-oidc-identityserver-part-4-d1c5651ad6c8)中，我们看到了从 IdentityServer 获取令牌的不同流程，然后在我们的 HTTP 调用中将这些令牌作为授权头传递，客户端应用程序能够按预期获取数据。

今天，我们将继续我们的旅程，了解更多关于用户和索赔的信息。所以，如果你是第一次接触…