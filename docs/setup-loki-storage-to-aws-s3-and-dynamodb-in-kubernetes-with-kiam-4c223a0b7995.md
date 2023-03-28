# 使用 Kiam 将 Loki 存储设置到 S3 AWS 和 Kubernetes 中的 DynamoDB

> 原文：<https://medium.com/codex/setup-loki-storage-to-aws-s3-and-dynamodb-in-kubernetes-with-kiam-4c223a0b7995?source=collection_archive---------1----------------------->

![](img/8a6063dbe323ec49e129c5d8e55fa3a7.png)

卢克·切瑟在 [Unsplash](https://unsplash.com/s/photos/monitoring?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

**Grafana**+**Prometheus**+**Loki**被我们用作监控系统，为我们的 Kubernetes 集群提供仪表盘、监控、警报和日志记录。

首先，Loki 将其数据(日志和索引数据)存储在 Kubernetes 集群内的持久卷(PV)上，其中 1TB 的 EBS(在 beta 环境中)连接到分配给 PV 的集群。