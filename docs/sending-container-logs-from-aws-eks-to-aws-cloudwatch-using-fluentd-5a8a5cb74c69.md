# 使用 Fluentd 将集装箱日志从 AWS EKS 发送到 AWS CloudWatch

> 原文：<https://medium.com/codex/sending-container-logs-from-aws-eks-to-aws-cloudwatch-using-fluentd-5a8a5cb74c69?source=collection_archive---------1----------------------->

集中式日志记录是当今现代云原生架构的关键方面之一。我们不希望我们的主要微服务除了执行业务逻辑之外，还要执行将日志写入集中位置的繁重任务，比如 AWS CloudWatch、Elasticsearch 或任何第三方 HTTPS 端点。作为最佳实践，这些繁重的任务，如重试、日志记录、安全(mTLS)等，应该从主要微服务中分离出来。