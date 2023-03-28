# 码头工人分发太空服

> 原文：<https://medium.com/codex/docker-distroless-space-suit-f00e6726f32e?source=collection_archive---------15----------------------->

**剧情简介:**如果你读了 [*这篇*](/@rockmetoo/chocolate-docker-container-65429e25fc3a) 的帖子，那么现在你可能已经知道容器就像一个巧克力盒子，单个 docker 图像就像一块巧克力。**通常**，单个 docker 容器包含以下内容:

1.  Linux 发行版文件(Ubuntu，CentOS，Debian 等。)
2.  运行时(Golang、Php、Python 运行时、glibc for C++)
3.  应用程序代码或二进制文件和配置文件

此外，容器中还包含`ls`、`grep`、`find`、`cat`甚至`bash`。你可以使用这个`root`登录你的 docker 容器…