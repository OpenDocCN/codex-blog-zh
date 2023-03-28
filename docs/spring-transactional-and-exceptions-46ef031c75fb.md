# Spring @事务性和异常

> 原文：<https://medium.com/codex/spring-transactional-and-exceptions-46ef031c75fb?source=collection_archive---------1----------------------->

## 为选中的异常配置自动回滚

![](img/22974d1961313b8f290b9e9356190f88.png)

最近，当我在做一个项目的时候，我发现了一个奇怪的“bug”(我最初的想法)。显然，在抛出异常后，我的一个事务没有自动回滚。我使用 [@Transactional](https://docs.spring.io/spring-framework/docs/current/reference/html/data-access.html#transaction-declarative-annotations) 的全部目的是利用自动回滚的好处，如果…