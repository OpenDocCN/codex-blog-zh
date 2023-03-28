# 如何使用 Nginx 作为反向代理

> 原文：<https://medium.com/codex/how-to-use-nginx-as-a-reverse-proxy-2d9ea5ebad24?source=collection_archive---------2----------------------->

## 为你的服务器建造一个看门人

![](img/a571663abc54c16ba0273bcbbcaeaef2.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Waldemar Brandt](https://unsplash.com/@waldemarbrandt67w?utm_source=medium&utm_medium=referral) 拍照

通常，当构建一个 web 应用程序时，您不会只构建一个应用程序。您有数据库、API、消息总线等等。
对你的服务的访问控制是至关重要的。
反向代理是处理这一问题的完美工具。Nginx 是充当反向代理的完美工具。在这里，我将向您展示如何设置 Nginx 作为一个反向…