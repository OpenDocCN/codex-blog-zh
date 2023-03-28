# EKS AWS 上的 Kubernetes 集群 IP 地址不足

> 原文：<https://medium.com/codex/kubernetes-cluster-running-out-of-ip-addresses-on-aws-eks-c7b8e5dd8606?source=collection_archive---------0----------------------->

![](img/22e921fc2300c6f80b1a133801d63b5f.png)

在 [Unsplash](https://unsplash.com/s/photos/node?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Manuel Sardo](https://unsplash.com/@manuelsardo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍照

你能想象有一天你在 AWS EKS 上的 Kubernetes 集群会遇到 IP 地址耗尽的问题吗？尽管您分配了一个足够大的 CIDR 块来托管所有的 pod，但是 CIDR 块的 IP 地址范围可能没有您想象的那么大。这就是我最近在我们的一个 Kubernetes 集群中遇到的情况。