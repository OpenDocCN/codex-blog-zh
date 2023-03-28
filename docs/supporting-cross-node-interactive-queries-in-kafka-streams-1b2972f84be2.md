# 支持 Kafka 流中的跨节点交互式查询

> 原文：<https://medium.com/codex/supporting-cross-node-interactive-queries-in-kafka-streams-1b2972f84be2?source=collection_archive---------6----------------------->

![](img/ce9338dbbd565134c83c52b06022f37b.png)

照片由[卡斯帕·卡米尔·鲁宾](https://unsplash.com/@casparrubin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

[Kafka Streams](https://kafka.apache.org/documentation/streams/) 是一个强大的工具，它在 Kafka 坚如磐石的基础设施之上添加了一个高级抽象，以支持构建流媒体应用程序。它有几个主要围绕两个概念的特性，`KStreams`表示无限的数据流，而`KTables`表示流数据的投影。甚至称这两个概念不同也不完全…