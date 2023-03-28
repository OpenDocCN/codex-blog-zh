# Kops 管理的 Kubernetes 集群中的 IRSA 实现

> 原文：<https://medium.com/codex/irsa-implementation-in-kops-managed-kubernetes-cluster-18cef84960b6?source=collection_archive---------0----------------------->

Kops 在 1.21 版本中引入了对 IRSA 的支持。在本指南中，我将分享我的配置方法。

![](img/ee1776b667bf8fef3e467e8934705efc.png)

照片由[阿迪·戈尔茨坦](https://unsplash.com/@adigold1?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/computer-security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

很长一段时间以来，我们的一些使用 AWS Boto3 Python 库的应用程序在试图使用 IAM 角色来利用 AWS 服务时会抛出`Credentials Not found`错误。这些应用程序在 Kops 引导的集群中作为 Kubernetes pods 运行。我们在跑步…