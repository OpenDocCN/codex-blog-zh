# Kubernetes 多租户(II) —设置租户应用

> 原文：<https://medium.com/codex/kubernetes-multi-tenancy-on-eks-ii-setup-tenant-apps-780aad7d721?source=collection_archive---------11----------------------->

![](img/8ba13448bdb9271a66bb019b1841445b.png)

照片由[丹尼·桑德曼](https://unsplash.com/@dannysunderman?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/appartment?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

我之前的帖子 [*用 AWS EKS 设置 Kubernetes 集群多租户*](/codex/setup-kubernetes-cluster-multi-tenancy-with-aws-eks-93561442aaeb) 讨论了如何在 Kubernetes 集群上设置基于名称空间的多租户环境。

完成之后，租户团队成员应该可以通过运行`kubectl`命令来访问他们自己在 Kubernetes 集群中的名称空间。租户团队的下一步可能是部署他们的应用程序…