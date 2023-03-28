# 用 Spring Cloud Sleuth 进行分布式跟踪

> 原文：<https://medium.com/codex/distributed-tracing-with-spring-cloud-sleuth-c441bddeaf3d?source=collection_archive---------1----------------------->

![](img/137b140494ebff47b95579a0bbaa3a16.png)

沃洛季米尔·赫里先科在 [Unsplash](https://unsplash.com/s/photos/investigator?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

春云侦探是春云伞下的项目之一。

该项目的目标是在分布式系统中启用跟踪，并使其在 Spring Boot 项目中易于配置。

在内部，Zipkin OSS 用于生成和报告跟踪。Zipkin 是一个 OSS，最初由 Twitter 开发，现在由……