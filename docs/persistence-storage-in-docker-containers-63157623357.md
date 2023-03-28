# Docker 容器中的持久性存储

> 原文：<https://medium.com/codex/persistence-storage-in-docker-containers-63157623357?source=collection_archive---------3----------------------->

## 如何在重启和重新部署之间保存容器数据

Docker 容器和其他容器技术默认是无状态的。那是什么意思？通俗地说，这意味着一个特定的物体在被摧毁后会失去它的状态。在 Docker 的上下文中，当运行在容器上的应用程序在运行时丢失了所有更改时，我们说容器是无状态的。