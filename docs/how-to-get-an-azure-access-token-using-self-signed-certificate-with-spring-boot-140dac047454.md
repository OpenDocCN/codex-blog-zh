# 如何使用 Spring Boot 的自签名证书获取 Azure 访问令牌

> 原文：<https://medium.com/codex/how-to-get-an-azure-access-token-using-self-signed-certificate-with-spring-boot-140dac047454?source=collection_archive---------6----------------------->

## 如何使用 Spring Boot 从 SSL 证书生成客户端断言

![](img/b0e6016908b12edafab5ac9faa04e2ce.png)

照片由[马体·米罗什尼琴科](https://www.pexels.com/@tima-miroshnichenko?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)从[佩克斯](https://www.pexels.com/photo/man-person-people-woman-6914069/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄——由作者编辑

大多数开发人员知道如何用`client_secret`认证他们的机密 web 客户端。

***但是证书客户端认证的流程是怎样的？我们如何认证客户端*** …