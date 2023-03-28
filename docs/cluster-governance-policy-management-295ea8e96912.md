# 集群治理—策略管理

> 原文：<https://medium.com/codex/cluster-governance-policy-management-295ea8e96912?source=collection_archive---------12----------------------->

## 使用 OPA 网关守护设备保护您的集群，并与 Kyverno 进行比较

让我们继续集群治理。

在我的上一篇文章中，我提到了一些需要强调的方面，特别是在安全性和资源管理方面，例如，授予不同级别的用户的权限，比如谁有权查看集群范围内的资源，以及创建或修改特定类型的资源。对于这些，Kubernetes 提供了 RBAC 来灵活地支持配置权限…