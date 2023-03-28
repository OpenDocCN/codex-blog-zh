# 我是如何用 C#在 Mongo 中设计开放式并发的

> 原文：<https://medium.com/codex/how-i-designed-optimistic-concurrency-in-mongo-with-c-8b62c8aabc40?source=collection_archive---------4----------------------->

处理您的交易

![](img/a84b6df45d5eb94fa1e51e3cb82d5713.png)

[阿卜杜勒·卡约姆](https://stock.adobe.com/uk/contributor/200563983/abdul-qaiyoom?load_type=author&prev_url=detail)在 [stock.adobe](https://stock.adobe.com/) 的照片

我一直喜欢使用 mongo 作为 NOSQL 数据库，当我开始学习 mongo 时，非常惊喜地发现它实际上可以处理[多文档事务](https://www.mongodb.com/docs/upcoming/core/transactions/)。

问:好，那么我们为什么需要一个并发控制？

答:虽然拥有事务将确保我们保存的操作是自动执行的，但我们必须确保**在读取、启动事务和保存之间的时间内，数据没有发生变化**。

## 定义

乐观并发控制是一种允许多个事务在互不干扰的情况下完成的控制。事务不会在资源中设置锁，因此不会阻塞数据库上的任何资源。它主要依赖于在提交之前验证没有其他事务修改过它所读取的数据。如果发现任何冲突的修改，则提交事务回滚。

阶段:

*   **开始**:记录时间戳。
*   **修改**:读取数据库资源，应用修改。
*   **验证**:检查数据是否被其他交易修改过。
*   **提交/回滚**:如果有冲突，中止事务。

## 设计概念

*   创建一个实施时间戳属性的基本实体
*   创建一个装饰器集合，在内部使用`IMongoCollection`并处理时间戳验证
*   创建一个助手类，用于轻松抛出一些异常

> 基本实体

基本实体有一个在构造函数中生成的时间戳，但是我们没有限制它的 setter 的可访问性，因为我们也将在 decorator 集合中改变它的值(你可以做的一件事是使它成为内部的，但是这取决于你的需要)

> 装饰类

decorator 类只是您在真实场景中需要的一小部分，但是它提供了一些我如何处理它的基本概念:

*   *CountAsync* :这里没有并发控制，我只是把 session 改为最后一个参数作为可选参数
*   *DeleteOneAsync* :这里我们首先检查结果是否被确认(必须将 [write concert 设置为多数](https://www.mongodb.com/docs/manual/reference/write-concern/))。如果失败，我实际上是在检查数据库中是否有一个条目，在这种情况下，写操作失败是因为**时间戳过滤器失败了。**这听起来像是一个昂贵的操作(但它只对失败的场景有效)
*   *DeleteManyAsync* :你可以看到我在这里使用了 BulkWrite 方法，这是因为我需要知道被删除的资源的数量。
*   ReplaceManyAsync:我在这里也使用了 BulkWrite，但是我在检查修改的资源的数量。

**请记住，所有操作都必须通过在我们正在更新的资源上创建过滤器来执行，过滤器必须包含时间戳。在保存之前，我们必须将时间戳更新为一个新值**

> 助手

ThrowHelpers 助手类是我喜欢使用的简单的东西，因为它使代码更加整洁