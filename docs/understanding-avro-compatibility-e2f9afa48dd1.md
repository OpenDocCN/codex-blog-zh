# 了解 Avro 兼容性

> 原文：<https://medium.com/codex/understanding-avro-compatibility-e2f9afa48dd1?source=collection_archive---------0----------------------->

![](img/59015dcf9f264135ad595821eccff17c.png)

托尔加·乌尔坎在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我最近越来越多地使用作为数据交换格式的 [Avro](https://avro.apache.org/docs/1.11.0/) 。Avro 旨在提供一种具有丰富数据结构的格式，一种快速而紧凑的二进制格式，与许多现成语言的集成，甚至是远程过程调用(RPC)能力。Avro 是一种与 Protobuf、Thrift、MessagePack 等在精神上非常相似的格式。他们都有相当相似的设计目标，只是完成他们的任务…