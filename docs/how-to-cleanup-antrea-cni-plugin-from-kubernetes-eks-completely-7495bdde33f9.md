# 如何从 Kubernetes (EKS)完全清除 Antrea CNI 插件

> 原文：<https://medium.com/codex/how-to-cleanup-antrea-cni-plugin-from-kubernetes-eks-completely-7495bdde33f9?source=collection_archive---------6----------------------->

![](img/11fae1a710df015dfbd296162b60b75f.png)

泰勒·维克在 [Unsplash](https://unsplash.com/s/photos/network?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

AWS 弹性 Kubernetes 服务(EKS)不支持 [Kubernetes 网络政策](https://kubernetes.io/docs/concepts/services-networking/network-policies/)及其 VPC CNI 插件。因此，EKS 支持网络政策有很多选择。

Antrea 是我在 EKS 的一个集群中探索和测试过的一种。Antrea 最吸引人的地方是支持基于 FQDN 的网络政策，这非常有用…