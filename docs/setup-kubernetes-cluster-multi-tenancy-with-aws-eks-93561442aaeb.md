# 使用 AWS EKS 设置 Kubernetes 集群多租户

> 原文：<https://medium.com/codex/setup-kubernetes-cluster-multi-tenancy-with-aws-eks-93561442aaeb?source=collection_archive---------13----------------------->

![](img/db255998f572e47a40ae46c4652483c6.png)

约书亚·索蒂诺在 [Unsplash](/t/technology?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## [抄本](http://medium.com/codex)

# 0.什么是多租户？

> 多租户集群由多个用户和/或工作负载共享，这些用户和/或工作负载被称为“租户”。多租户集群的操作员必须将租户相互隔离，以最大限度地减少受损或恶意租户对集群和其他租户造成的损害。还有，集群…