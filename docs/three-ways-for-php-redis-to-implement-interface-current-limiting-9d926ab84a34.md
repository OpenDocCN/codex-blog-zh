# PHP+Redis 实现接口限流的三种方法

> 原文：<https://medium.com/codex/three-ways-for-php-redis-to-implement-interface-current-limiting-9d926ab84a34?source=collection_archive---------4----------------------->

## 界面电流限制

![](img/6631f579ffccceb589eb78ec297afb92.png)

由 [OPPO Find X5 Pro](https://unsplash.com/@oppofindx5pro?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在分布式高可用性系统中，节流是一项必要的操作。

这个流量可以是网络流量、带宽、每秒事务数、每秒请求数、并发请求数或业务指标。