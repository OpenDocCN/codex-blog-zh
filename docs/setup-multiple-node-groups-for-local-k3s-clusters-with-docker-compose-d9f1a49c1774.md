# 使用 Docker-Compose 为本地 K3s 集群设置多个节点组

> 原文：<https://medium.com/codex/setup-multiple-node-groups-for-local-k3s-clusters-with-docker-compose-d9f1a49c1774?source=collection_archive---------1----------------------->

![](img/8ff65687ffd60dcf715c43ab452400e6.png)

照片由[帕帕约安努·科斯塔斯](https://unsplash.com/@papaioannou_kostas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/node-group?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

K3s 是一个轻量级的 Kubernetes 发行版，非常适合 Edge、IoT、CI、ARM 等，因此在为主要用于测试的应用程序构建本地集成环境时非常有用。

我之前的[帖子](/codex/setup-local-integration-environment-with-k3s-and-docker-compose-13fd815765cc)介绍了用 K3s 和 docker-compose 建立集成环境的详细步骤，这是 K3s 的一个简单模式…