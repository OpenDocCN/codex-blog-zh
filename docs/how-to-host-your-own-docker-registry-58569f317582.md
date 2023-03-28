# 如何托管自己的 Docker 注册表

> 原文：<https://medium.com/codex/how-to-host-your-own-docker-registry-58569f317582?source=collection_archive---------9----------------------->

## 退出 Kubernetes Kubeadm，转而使用 MicroK8s 第二部分

![](img/453092093a15b40f7a4c4e764bdc6aec.png)

Maksym Kaharlytskyi 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在今天的文章中，我将继续用 MicroK8s 重建我的 Kubernetes 集群。如果你没有读第一部分，你可以在这里找到。概括地说，我最初使用的是 Kubeadm，但是在安装了法兰绒网络之后，CPU 使用率几乎…