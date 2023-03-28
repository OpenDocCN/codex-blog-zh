# 使用 K3s 和 Docker Compose 设置本地集成环境

> 原文：<https://medium.com/codex/setup-local-integration-environment-with-k3s-and-docker-compose-13fd815765cc?source=collection_archive---------1----------------------->

![](img/15af6f90704b7124dc78791353d596a1.png)

瓦里克·切尔内茨基在 [Unsplash](https://unsplash.com/collections/4480531/construction?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

越来越多的应用程序以容器映像的形式发布，并部署到 Kubernetes 集群中。因此，一个方便的集成环境对于开发人员进行本地测试、调试、故障排除等非常有帮助。

这篇文章提供了一个使用 K3s 和 Docker compose 在本地快速构建集成环境的解决方案。