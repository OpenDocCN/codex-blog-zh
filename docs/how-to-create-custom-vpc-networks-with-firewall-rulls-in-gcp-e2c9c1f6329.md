# 如何在 GCP 创建带有防火墙规则的自定义 VPC 网络

> 原文：<https://medium.com/codex/how-to-create-custom-vpc-networks-with-firewall-rulls-in-gcp-e2c9c1f6329?source=collection_archive---------1----------------------->

## [法典](http://medium.com/codex)

虚拟专用云(VPC)网络是物理网络的虚拟版本，在谷歌的生产网络内部实现，使用[仙女座](https://www.usenix.org/system/files/conference/nsdi18/nsdi18-dalton.pdf)。VPC 网络提供以下服务:

*   为您的[计算引擎虚拟机(VM)实例](https://cloud.google.com/compute/docs/instances)提供连接，包括[谷歌 Kubernetes 引擎(GKE)集群](https://cloud.google.com/kubernetes-engine/docs/concepts/cluster-architecture)、 [App Engine 灵活环境](https://cloud.google.com/appengine/docs/flexible)实例，以及基于计算引擎虚拟机构建的其他谷歌云产品。
*   提供本机内部 TCP/UDP 负载平衡和…