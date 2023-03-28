# 良好做法。节流那个昂贵的 API！

> 原文：<https://medium.com/codex/throttle-that-expensive-api-40977b583735?source=collection_archive---------20----------------------->

![](img/6ce4a8772a7c0f719c3f8ff4905b5947.png)

照片由[克里斯·莱佩尔特](https://unsplash.com/@cleipelt?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

W 你在生产环境中做过最糟糕的事是什么？我想我做了很多被提到的事情。我以 root 用户身份登录时，在生产服务器的根文件夹中执行了`rm -rf`。不存在备份。从此以后，我默认以 root 用户身份登录。我不小心刷新了整个生产数据库，因为我以为这是一个 UAT 环境。我有一个 12 小时大的…