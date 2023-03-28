# K8s 容器中的时区

> 原文：<https://medium.com/codex/timezones-in-k8s-container-ca22f24d2f42?source=collection_archive---------14----------------------->

![](img/c97c22115fef25511ca00a1d62c7a59d.png)

我正在跟踪一些 EOD 的作业状态，以检查它是否比预定时间晚开始，或者超时。使用 Golang 作为 Prometheus 客户端程序创建，收集的数据作为 Prometheus gauge metric，以表格格式显示在 Grafana 仪表板中。

在我的笔记本电脑上完美地工作和测试。

然而，我遇到了我们经常遇到的典型问题。是的，它在…