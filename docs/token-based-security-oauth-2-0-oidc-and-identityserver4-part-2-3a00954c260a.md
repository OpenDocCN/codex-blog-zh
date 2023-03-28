# 基于令牌的安全性、OAuth 2.0、OIDC 和身份服务器 4 —第 2 部分

> 原文：<https://medium.com/codex/token-based-security-oauth-2-0-oidc-and-identityserver4-part-2-3a00954c260a?source=collection_archive---------2----------------------->

![](img/794b0a74610713b75a0835d8af54a612.png)

Philipp Katzenberger 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 介绍

在我的[上一篇](/codex/token-based-security-oauth-2-0-oidc-and-identityserver4-33b19b92d169)中，我们了解到 OAuth 是一个专门为**HTTP API**打造的**授权框架**。它允许用户安全地将**范围的 API 访问**委托给应用程序。

范围访问意味着，用户确切地定义他们希望应用程序被允许使用 API 的哪些部分。这个…