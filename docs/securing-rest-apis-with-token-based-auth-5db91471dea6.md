# 使用基于令牌的授权保护 REST APIs

> 原文：<https://medium.com/codex/securing-rest-apis-with-token-based-auth-5db91471dea6?source=collection_archive---------5----------------------->

![](img/d8e25bc2926906661973166df7d5babe.png)

图片由[迭戈·委拉斯开兹](https://pixabay.com/users/Templune-1493489/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2226631)来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2226631)

Auth 是一个服务层，它进行身份验证以识别用户是否是他们所声称的那个人，并进行授权以检查用户是否有权访问指定的 REST API。最常见的身份验证形式是使用用户名和密码，但嵌入到应用程序或脚本中很麻烦，也是一个安全问题。更常用的身份验证形式是使用令牌来表示…