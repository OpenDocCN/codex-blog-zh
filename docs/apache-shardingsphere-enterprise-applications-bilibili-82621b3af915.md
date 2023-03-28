# Apache ShardingSphere 企业应用程序—哔哩哔哩

> 原文：<https://medium.com/codex/apache-shardingsphere-enterprise-applications-bilibili-82621b3af915?source=collection_archive---------28----------------------->

> 为了进一步理解应用程序场景和企业需求，并提高开发团队对 Apache ShardingSphere 的理解，我们的社区推出了“企业访问”系列。

![](img/ee97efcf65ea46b3cfbb86c52503370d.png)

[Apache ShardingSphere](https://shardingsphere.apache.org/) 的核心贡献团队被邀请到[哔哩哔哩](https://www.bilibili.com/)的总部。我们的 PMC 主席张亮与哔哩哔哩的技术团队讨论了电子商务和数字娱乐垂直应用场景，以及不同版本的 ShardingSphere 的功能。

随着数据量的空前增长和数据应用场景的日益多样化，不同的平台、业务和场景导致了数据库应用的碎片化。数据库今天面临的挑战与它们被设想要解决的挑战完全不同。这也反映在哔哩哔哩电子商务业务的增长上。

十多年来，哔哩哔哩已经建立了一个以用户、创作者和内容为中心的生态系统社区，同时还制作高质量的视频。随着用户数量的增加，哔哩哔哩也逐渐发展了周边商业生态系统，如其订阅收入模式。业务线和应用场景的扩展给哔哩哔哩的技术团队带来了巨大的挑战，尤其是在后端数据的管理和应用方面。

在访问期间，我们的社区和哔哩哔哩主要讨论了这三点:

## 与哔哩哔哩联合打造的 SQL 预热功能:

当哔哩哔哩在其业务中查询具有批处理优先级的货物和订单时，通常需要在初始链接过程中手动预热 SQL 以提高整体性能。但是在手动预热的过程中，由于使用了 [Mybatis](https://mybatis.org/mybatis-3/index.html) 框架的`foreach`语法，不同长度的参数不能作为 SQL 来预热。

Apache ShardingSphere 可以通过 `preview`看到 SQL 执行计划，从而预热 SQL。未来，我们计划提供一个单独的 SQL 预热接口，并将 SQL 预热时间合并到启动时间中。SQL 预热后，ShardingSphere 将自行启动。

Apache ShardingSphere 是一个由社区需求驱动的开源项目。目前，许多功能的开发是由特定的用户需求驱动的，这些需求在开发后反馈给社区，并逐渐集成到 Apache ShardingSphere 中。

因此，ShardingSphere 社区邀请哔哩哔哩的技术团队参与 SQL 预热功能。

随着 Apache ShardingSphere 应用场景的扩展，对 ShardingSphere 适应各种特殊业务场景的能力有了更高的期待。在之前的“企业访问”系列中，ShardingSphere 社区认识到，尽管它有 100 多个功能模块，但来自各个垂直行业的企业对 ShardingSphere 有不同的期望。

## 哔哩哔哩在交通高峰情况下的断路器自动化:

随着哔哩哔哩电子商务规模的增长，正如许多电子商务企业经常出现的情况一样，开始采用现金回扣和闪购等前端策略，以提高用户保留率。

而在后端，当仓库的返利性能等于并发交易数同时超过自身连接数限制时，只能通过 DBA 基于业务级 SKU 数手动断开来停止。手动干预效率低下，消耗 DBA 的精力，因此 [ShardingSphere-Proxy](https://shardingsphere.apache.org/document/current/en/quick-start/shardingsphere-proxy-quick-start/) 有望提供自动断路器功能。

然而，flash 交易场景要求太高，因此 [Redis](https://redis.io/) 仍然是一个更好的选择。实现自动化只需要设置规则和阈值。

将数据下沉到代理也可以实现 Redis 的能力，但是无论上层怎么变，数据库底层都不会变。所以 DBA 还是主动操作断路器机制比较好。否则，设置阈值后，如果应用程序与数据库的连接稍有超时，就会瞬间切断大量事务，很容易造成业务中断。

目前比较好的方法是挖掘出各种关键信息，并基于代理显示在可视化界面上，方便 DBA 实时对比和操作，而不是实现自动化。

## Apache ShardingSphere 注册表:

在 Apache ShardingSphere 架构中，注册中心提供分布式治理能力，并对用户完全开放，因为其计算节点( [Shardingsphere-proxy](https://shardingsphere.apache.org/document/current/en/quick-start/shardingsphere-proxy-quick-start/) )是无状态的，没有数据存储能力。因此，用户帐户和授权信息需要存储在注册表中。

同时，在注册表的帮助下，Apache ShardingSphere 可以将信息实时分发到集群中的多个计算节点，大大降低了用户使用集群时的维护成本，提高了管理效率。

在集群模式下，Apache ShardingSphere 集成了第三方注册组件 [ZooKeeper](https://zookeeper.apache.org/) 和 [Etcd](https://etcd.io/) 来实现集群环境下的元数据和配置共享。同时借助注册表的通知和协调能力，保证了共享数据发生变化时集群的实时同步。并且企业不会意识到来自注册中心的改变。

# 与哔哩哔哩的问答

**问:使用** [**JDBC**](https://shardingsphere.apache.org/document/current/en/overview/#shardingsphere-jdbc) **连接到治理中心会有性能损失吗？**

答:不会，它只在初始化的时候连接到治理中心，有变化的时候治理中心会发推送。

**问:**[**sys bench**](https://wiki.gentoo.org/wiki/Sysbench)**如何对 Apache ShardingSphere 进行压力测试？**

答:Apache ShardingSphere 的两种部署类型，JDBC 和代理，都是由 Sysbench 测试的。ShardingSphere 在 JDBC 端有一个新设计的类似 Sysbench 的 Java 程序，可以用来对 JDBC 和代理进行压力测试。也能保证官方的 Sysbench 和我们的 Java 程序共享同一个标准。

**问:ShardingSphere 能收敛连接数吗？**

答:ShardingSphere-Proxy 可以收敛连接数，但肯定会导致性能损失。

**问:代理可以识别慢速 SQL 吗？**

答:目前开源版本不支持该功能，因为开源版本的用户大多是互联网企业，对性能损失的容忍度较低。因此，探针的数量相对较少。

**问:**[**elastic job**](https://shardingsphere.apache.org/elasticjob/)**是否属于 Apache ShardingSphere？**

答:ElasticJob 目前被 Apache ShardingSphere 用作迁移工具。此外，ElasticJob 也可用于活性探针。

**问:互联网企业是否在大规模使用代理？**

答:大多数互联网用户选择混合部署模式，使用 JDBC 进行开发和提高性能，使用代理进行管理。金融客户更喜欢使用代理，因为他们可以将代理作为统一管理的数据库，而无需额外的学习成本。

问:我们目前使用的是 ShardingSphere 版本 4.1.1，它对事务有什么支持？

答:4.11 和 5.1.0 版本都支持 [XA](https://docs.oracle.com/database/121/TTCDV/xa_dtp.htm#TTCDV327) 分布式事务管理。我们计划在今年下半年开发全球交易管理器( [GTM](https://docs.oracle.com/cd/E17276_01/html/programmer_reference/xa_build.html) )。

## 与我们联系

如果您已经在您的业务中应用了 Apache ShardingSphere 解决方案，或者如果您想快速了解和介绍 Apache ShardingSphere 5。x 生态系统，您可能希望我们社区中的某个人来帮助您，并与您的团队分享 Apache ShardingSphere 技术。

请随时通过我们的官方社区渠道联系我们，如 Twitter 或 Slack。

如果我们都认为 ShardingSphere 适合您的业务场景，我们的社区团队将很乐意与您和您的工程师联系，回答他们的问题。

# Apache ShardingSphere 项目链接:

[ShardingSphere Github](https://github.com/apache/shardingsphere/issues?page=1&q=is%3Aopen+is%3Aissue+label%3A%22project%3A+OpenForce+2022%22)

[ShardingSphere Twitter](https://twitter.com/ShardingSphere)

[切割球松弛度](https://join.slack.com/t/apacheshardingsphere/shared_invite/zt-sbdde7ie-SjDqo9~I4rYcR18bq0SYTg)

[投稿指南](https://shardingsphere.apache.org/community/cn/contribute/)