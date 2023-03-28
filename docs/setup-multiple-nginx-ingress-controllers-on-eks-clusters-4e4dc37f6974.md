# 在 EKS 集群上设置多个 Nginx 入口控制器

> 原文：<https://medium.com/codex/setup-multiple-nginx-ingress-controllers-on-eks-clusters-4e4dc37f6974?source=collection_archive---------0----------------------->

![](img/afe60c9db365488ac3ab9ebc4240cd6e.png)

萨姆·穆卡达姆在 [Unsplash](https://unsplash.com/s/photos/gateway?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

Kubernetes 网络对于管理 Kubernetes 集群至关重要，因为对于大多数 Kubernetes 集群来说，内部运行的应用程序需要与外部的其他实体进行通信。

对于在 Kubernetes 集群中运行的服务，出于安全和管理的目的，我们不希望它们都直接暴露给外部的互联网公众访问。