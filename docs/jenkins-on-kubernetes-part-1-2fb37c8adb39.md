# 詹金斯谈库本内特:第 1 部分

> 原文：<https://medium.com/codex/jenkins-on-kubernetes-part-1-2fb37c8adb39?source=collection_archive---------0----------------------->

## 科技基础，[法典](http://medium.com/codex)

> 在这篇文章中，我将介绍在我使用 kubeadm 构建的 Kubernetes 集群上部署 Jenkins 的基本步骤。k8s 集群将托管 Jenkins 和代理来运行管道。我假设你对 Kubernetes 和 Jenkins 的概念有很好的理解。执行所有步骤都需要一个有效的 Kubernetes 集群和 kubectl。

`**Kubernetes**`是一个开源的容器编制器。它是一个以容器为中心的应用程序，已经成为部署和操作容器化应用程序的事实标准。现代…