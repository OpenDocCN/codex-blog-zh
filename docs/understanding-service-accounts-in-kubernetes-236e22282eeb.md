# 了解 Kubernetes 中的服务帐户

> 原文：<https://medium.com/codex/understanding-service-accounts-in-kubernetes-236e22282eeb?source=collection_archive---------5----------------------->

![](img/42fd08c53da7a1232e23fe8de184eb65.png)

[连浩曲](https://unsplash.com/@lianhao?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

这篇文章是我将讨论 Kubernetes 安全性的系列文章的一部分。目的是让你和我一起简化这个复杂的概念。

与 Kubernetes 集群的交互包括与 API 服务器的通信。这种通信以请求的形式发送到 API 服务器。当 API 服务器收到请求时，它首先尝试验证…