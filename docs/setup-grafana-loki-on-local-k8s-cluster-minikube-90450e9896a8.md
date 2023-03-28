# 在本地 K8s 群集 Minikube 上设置 Grafana/Loki

> 原文：<https://medium.com/codex/setup-grafana-loki-on-local-k8s-cluster-minikube-90450e9896a8?source=collection_archive---------0----------------------->

![](img/549b3c95c69cb0445228ba93db3d5d5f.png)

安德鲁·庞斯在 [Unsplash](https://unsplash.com/s/photos/files?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我最近在 Minikube 集群上本地设置了 Loki，用于测试和验证目的。我发现网上的一些介绍已经过期了，所以它们不能正常工作。

因此，在这篇文章中，我想分享我在 Minikube 集群上安装 Loki 和 Grafana 并将它们连接在一起的详细步骤。

# 洛基是什么？