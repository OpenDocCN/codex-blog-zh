# 探索客户走向告密者模式

> 原文：<https://medium.com/codex/explore-client-go-informer-patterns-4415bb5f1fbd?source=collection_archive---------1----------------------->

## 调用 Kubernetes 资源而不使集群过载

![](img/22d37896d19de02d6048032c69c332b8.png)

来自 Unsplash @ [里奇叔叔](https://unsplash.com/photos/La5V_Qr6h3A)

在平台开发中，我们的集群运行涉及多个团队和各种 GCP 资源的操作符，对于这些查询我们经常需要编写各种代码，包括但不限于由 kubectl、最常用的 Java [fabric8 API](https://github.com/fabric8io/kubernetes-client) lib 和 go 程序组成的 bash 脚本，如……