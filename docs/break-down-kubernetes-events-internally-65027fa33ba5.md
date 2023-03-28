# 在内部分解 Kubernetes 事件

> 原文：<https://medium.com/codex/break-down-kubernetes-events-internally-65027fa33ba5?source=collection_archive---------6----------------------->

## [法典](http://medium.com/codex)

## 理解事件结构，并了解它们是如何产生的

![](img/14661f37ebecb914fdb804a83b3b9c58.png)

图片来自 Unsplash [@bizarretribe](https://unsplash.com/photos/2nJPhGYXtNI)

`kubectl get events --namespace {name}`

它被认为是调试集群中对象的基本 CLI 命令之一。我们通常可以用`kubectl get pod` 或`kubectl logs pod xxx`解决简单的问题。但是什么…