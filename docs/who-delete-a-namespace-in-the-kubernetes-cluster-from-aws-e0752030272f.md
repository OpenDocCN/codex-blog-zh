# 谁从 AWS 中删除了 Kubernetes 集群中的名称空间？

> 原文：<https://medium.com/codex/who-delete-a-namespace-in-the-kubernetes-cluster-from-aws-e0752030272f?source=collection_archive---------2----------------------->

![](img/d5e019e53ec4a030ed4005795474756c.png)

[自由股票](https://unsplash.com/@freestocks?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/hacker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在我们的一个 Kubernetes 集群中，一个开发团队拥有的名称空间被删除，包括名称空间中的 Kubernetes 资源，如 Pods、部署、服务等。

**发生了什么？谁干的？如何避免？**

在这篇文章中，我将介绍故障排除过程，展示如何找出根本原因。还有，一些想法…