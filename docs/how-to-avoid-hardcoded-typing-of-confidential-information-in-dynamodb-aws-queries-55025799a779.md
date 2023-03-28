# 如何避免在 Dynamodb AWS 查询中硬编码输入机密信息

> 原文：<https://medium.com/codex/how-to-avoid-hardcoded-typing-of-confidential-information-in-dynamodb-aws-queries-55025799a779?source=collection_archive---------24----------------------->

大家好！在这篇博客中，我们将学习如何避免键入我们的硬编码凭证，其中可能包括机密信息，如我们的`secret_access_key`。

![](img/823ec3a0451fc4ac197eafe344ade28c.png)

由[帕韦尔·切尔文斯基](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

短语“NoSQL 数据库”经常被用来描述任何非关系数据库。无论如何，大多数人都同意 NoSQL 数据库存储数据的格式不同于关系表。

NoSQL 数据库的出现恰逢存储成本大幅下降。不得不构建复杂、具有挑战性的数据模型来防止数据重复的日子已经一去不复返了。NoSQL 数据库的设计考虑到了开发人员的生产力，因为开发人员开始承担开发软件的大部分费用。

> Amazon DynamoDB 是一个键值和文档数据库，在任何规模下都能提供一位数的毫秒级性能。这是一个全面管理的、多区域、多主、耐用的数据库，具有内置的安全性、备份和恢复以及适用于互联网规模应用程序的内存缓存。DynamoDB 每天可以处理超过 10 万亿个请求，每秒可以支持超过 2000 万个请求的峰值。
> 
> amazonaws.cn

文档数据库将信息保存在类似 JSON 对象的文件中。每个文档中的字段和值都是成对的。一种更简单的数据库类型是键-值数据库，其中每个对象都有键和值。表、行和动态列将数据存储在宽列存储中。数据存储在图形数据库的节点和边中。边包含关于节点之间连接的信息，节点通常保存关于人、位置和对象的数据。

连接到 Dynamodb 数据库时，您可能会遇到以下脚本:

出于安全考虑，您不能将`secret_access_key`直接添加到代码中。

如果您在一个 *Amazon EC2* 实例上执行您的脚本，您必须给该实例一个 *IAM* 角色；然后它将被授予自己的证书。

在 AWS *命令行界面* (CLI)中，如果在您的机器上运行该代码，您需要使用 **configure** 命令将凭证和区域保存在本地配置文件中。该配置将保存在**凭证**目录中。您可以使用下面的代码行来实现您的配置。

# 结论

终于！使用上面的方法，我们可以隐藏我们的`secret_access_key`不被发现。感谢您的阅读，我会继续添加新的说明和示例。关于其他文章和更新，请通过 LinkedIn 与我联系。你也可以在 [Github](https://github.com/bkullukcu) 上关注我。