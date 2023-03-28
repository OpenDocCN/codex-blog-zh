# 带有 Kotlin 的新 Spring 授权服务器

> 原文：<https://medium.com/codex/the-new-spring-authorization-server-with-kotlin-20ccf47f2274?source=collection_archive---------2----------------------->

## 新 Spring 项目中 OAuth 2 授权的实践调查

![](img/19b10daa2f6096e87e92c8d113f9063c.png)

照片由 [Gin Patin](https://www.pexels.com/de-de/@gin-patin-1357542?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 从 [Pexels](https://www.pexels.com/de-de/foto/drei-personen-sitzen-auf-grunem-grossem-netz-2692556/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

E 即使这两个过程经常混淆，*认证和授权*描述了两个不同的过程。

**认证**描述了识别*谁*访问资源的过程，**授权**另一方面描述了*什么* …