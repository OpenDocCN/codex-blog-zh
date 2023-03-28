# 如何管理 AWS ElasticSearch 上的索引

> 原文：<https://medium.com/codex/index-management-on-aws-elasticsearch-36cc788ec06e?source=collection_archive---------4----------------------->

..经历了暖，冷，删的循环！

![](img/36fdcc9e03d87ccf1f28c1b928305a50.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

ElasticSearch 是 ElasticSearch-LogStash-Kibana 或俗称 ELK stack 的重要组成部分。ELK stack 是一种流行的机制，用于日志聚合、分析和监控应用程序日志和应用程序记录的事件。这些日志被处理并存储在索引中，随着时间的推移，这些索引会变大并开始…