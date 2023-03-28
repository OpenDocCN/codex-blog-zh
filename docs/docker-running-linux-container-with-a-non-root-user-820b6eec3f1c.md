# Docker:用非根用户运行 Linux 容器

> 原文：<https://medium.com/codex/docker-running-linux-container-with-a-non-root-user-820b6eec3f1c?source=collection_archive---------0----------------------->

## 技术基础，[法典](http://medium.com/codex)

![](img/48aba2804dc88e2bfbcf236e417b1676.png)

[Justus Menke](https://unsplash.com/@justusmenke?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 第 1 部分:作为进程的 Linux 容器

现代 DevOps 周期在不断发展，我们应该为此感谢容器。容器的概念在 Linux 操作系统下诞生了。Linux 容器是一个独立于系统其余部分的进程。正如我所说，这是一个过程…