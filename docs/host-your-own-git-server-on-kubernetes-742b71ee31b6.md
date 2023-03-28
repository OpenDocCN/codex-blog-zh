# 在 Kubernetes 上托管自己的 Git 服务器

> 原文：<https://medium.com/codex/host-your-own-git-server-on-kubernetes-742b71ee31b6?source=collection_archive---------4----------------------->

## 在 10 分钟或更短时间内启动并提交！

![](img/686ef164a96125bac90a644b8e320e3f.png)

罗曼·辛克维奇·🇺🇦在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

本周早些时候，当我在我的 Kubernetes 集群上修补一些东西时，我有了一个惊人的发现。目前，我有一个 Docker 注册表、一个 Python 包索引和 Jenkins。但是我没有对我的代码进行任何形式的版本控制。幸运的是，这个问题可以用 Gogs 轻松解决。简单来说，Gogs 是一个…