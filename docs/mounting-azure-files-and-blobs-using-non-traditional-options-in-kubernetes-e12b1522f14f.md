# 在 Kubernetes 中使用非传统选项挂载 Azure 文件和 Blobs

> 原文：<https://medium.com/codex/mounting-azure-files-and-blobs-using-non-traditional-options-in-kubernetes-e12b1522f14f?source=collection_archive---------2----------------------->

## 库伯内特咖啡馆，[抄本](http://medium.com/codex)

![](img/f2b0a824eef239de92fcca1c57aeaf5d.png)

由[桑德·莱奈茨](https://unsplash.com/@sanderlenaerts?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 库伯内特斯的存储

Kubernetes 是容器工作负载的协调器。容器是短暂的，所以在任何用例中，当你的应用程序需要持久化数据的时候，抽象就出现了。库伯内特卷在其…