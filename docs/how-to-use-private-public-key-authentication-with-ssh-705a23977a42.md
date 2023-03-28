# 如何通过 SSH 使用私钥/公钥认证

> 原文：<https://medium.com/codex/how-to-use-private-public-key-authentication-with-ssh-705a23977a42?source=collection_archive---------4----------------------->

## 故意忘记你的密码！

![](img/6cbaaae51afe233c8db5e21a153901a4.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[Towfiqu barb huya](https://unsplash.com/@towfiqu999999?utm_source=medium&utm_medium=referral)拍摄的照片

当使用 web 应用程序时，您经常需要通过 SSH 登录到您的服务器。因此，多台服务器需要不同的密码。
通过使用私有/公共密钥，你可以通过 SSH 登录到你的系统，而不需要记住所有的密码。在这里，我将向您展示如何做到这一点。