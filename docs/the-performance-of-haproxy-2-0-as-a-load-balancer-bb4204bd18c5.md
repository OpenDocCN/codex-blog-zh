# HAProxy 2.0 作为负载平衡器的性能

> 原文：<https://medium.com/codex/the-performance-of-haproxy-2-0-as-a-load-balancer-bb4204bd18c5?source=collection_archive---------6----------------------->

## [法典](http://medium.com/codex)

![](img/835a7a0087be270ee0cc16373a3ae518.png)

[摄](https://unsplash.com/@thenigmatic?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 Nigmatic

# 介绍

HAProxy 是一个前端负载平衡器，它将客户端请求转发到后端服务器。在这篇文章中，我们将讨论新版本的 HAProxy 2.1，以及我对 HAProxy 性能的一些基准测试。

# 什么是 HAProxy？