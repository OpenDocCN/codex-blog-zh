# 在 Datadog 中收集 AWS 指标

> 原文：<https://medium.com/codex/collect-aws-metrics-in-datadog-3bb787ad684d?source=collection_archive---------14----------------------->

![](img/a0d6301153184e9bca40f70d2a730f78.png)

弗雷德里克·科贝尔在 [Unsplash](https://unsplash.com/s/photos/monitor?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

Datadog 在用于系统观察时非常流行，它支持各种数据源，如指标、日志。这对于 IT 运营、安全和开发团队从不同角度监控他们的系统非常有帮助。

为了将 AWS 资源的指标收集到 Datadog 中，需要集成 Datadog 和 AWS 帐户，并且应该在 Datadog 和 AWS 两端进行设置。这个帖子…