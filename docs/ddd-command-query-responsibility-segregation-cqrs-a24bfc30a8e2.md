# DDD —指挥查询责任分离(CQRS)

> 原文：<https://medium.com/codex/ddd-command-query-responsibility-segregation-cqrs-a24bfc30a8e2?source=collection_archive---------6----------------------->

![](img/22d937d17a1a84ed898582676b9ae97f.png)

汉娜·乔舒亚在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 命令很复杂，查询很简单

之前我们已经看了有状态的 DDD [实体](https://phillipjohnson.co.uk/ddd-entity-and-value-types)，以及状态改变的[事件](https://phillipjohnson.co.uk/ddd-events-are-complex)。为了降低复杂性，我们可以具体说明什么具有状态，并封装它在哪里发生变化。事件是高层代码，位于洋葱架构的中间。在这篇文章中，我们将看看如何降低…