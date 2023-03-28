# Sequelize、Postgres、JSON 和 JSONB

> 原文：<https://medium.com/codex/sequelize-postgres-json-jsonb-c1d4a80a0a51?source=collection_archive---------4----------------------->

这里是我在使用 sequelize 时遇到的一些问题。把它们写下来，希望能为别人节省几分钟。

Sequelize 对于 node 来说是一个非常棒的 ORM，它给了我强烈的活跃记录的感觉，我喜欢它！

![](img/a51a99d775573998b99b9299f5d97c66.png)

它的特点是

*   种子和迁移。
*   关联模型的急切加载和迟缓加载。
*   挂钩(生命周期方法/回调)
*   发电机(npx 序列-cli)

## 同步您的模型

sequelize 相对于 active record 的一个特性是将你的模型“同步”到数据库的能力。实际上，它生成了一个模式并将其应用到数据库中(尽管如果也生成一个模式文件会很酷，可能我还没有发现)。

为什么有意思？在 rails 世界中，您可以生成模型和迁移，并在进行过程中迁移数据库。在生产中，这是正确的做法。但是对于一个在基础上快速迭代的新项目，这就有点矫枉过正了。一旦您使用了 v1 或更早的版本，您将希望开始使用迁移。但是，虽然您的项目只存在于 localhost 上，但是最好有一个地方来进行更改(模型)并传播这些更改。

这就是同步所做的。

docs:[https://sequel ize . org/master/manual/model-basics . html # model-synchron ization](https://sequelize.org/master/manual/model-basics.html#model-synchronization)

有强制、改变或仅创建缺失表的选项。到目前为止，我已经使用了 force，但是在 env 变量后面设置了这个特性，这样它就不会在每次代码更改时清除数据库(使用 nodemon 进行重载)。

不会把它放在我的产品代码附近，但在早期开发阶段，我喜欢它。它让对模型的想象过程更加即时。

## 可能的改进:

虽然 sync 在开发中很棒，但是如果有更清晰的从 sync 切换到 migrations 的路径就更好了。如前所述，如果 sync 可以在某个时间点(当您过渡到生产、版本控制或引入更多开发人员时)生成一个模式(或直接迁移)，这将是一个很好的工作流。

目前，当谈到迁移和同步时，感觉就像是另一个命题，在它们之间不可避免地会有一个转换点。当您更改模型并同步它时，使用生成器生成模型(它生成相应的迁移)会立即脱离“同步”。我希望生成我的模型，对它们进行处理，然后在稍后阶段从这些模型生成迁移。

种子也有类似的感觉，因为生成的种子语法利用 SQL 查询来创建和删除种子数据，而我更喜欢在初始开发期间使用我的模型来这样做，然后在稍后的阶段生成种子/设备。肯定取决于数据量和个人喜好，所以里程会有所不同。

有人可能已经解决了这些问题，只是我还没有找到，如果你有更好的方法在 twitter 上联系我。

# Postgres JSON vs JSONB

但是尽管 sync 很酷，JSON vs JSONB 却非常烦人，直到我发现我做错了什么。

**注意:**使用 sequelize，postgres & `DataTypes.JSON`时你的数据实际上是*存储为明文，而不是 json。*

*这导致了各种烦人的解析&将 json 字符串化到数据库内外。*

*在我意识到实际发生了什么之前，我在想‘他们到底为什么认为这是个好主意？’，我将设置 get() & set()方法来解析 json。*

*我没有选择 JSONB，因为我认为我不需要直接查询这些字段，所以这看起来有些多余。我想我可以很快地在任何阶段切换它。*

*再次查阅文档后，我看到:*

# *PostgreSQL 注意事项*

*PostgreSQL 中的 JSON 数据类型将值存储为纯文本，而不是二进制表示。如果您只是想存储和检索 JSON 表示，使用 JSON 将占用更少的磁盘空间，并且从它的输入表示进行构建所需的时间也更少。但是，如果您想对 JSON 值进行任何操作，您应该首选下面描述的 JSONB 数据类型。*

*文件:[https://sequelize.org/master/manual/other-data-types.html](https://sequelize.org/master/manual/other-data-types.html)*

***另请注意:JSONB 仅适用于 Postgres***

*因此，如果您不需要直接查询 JSON 中的数据，那么使用 JSON 似乎有一些性能上的好处。但是如果您选择使用 JSON，您将需要管理数据库中的序列化数据(只有几行代码)。*