# ShardingSphere-JDBC 驱动发布:一个不需要修改代码的 JDBC 驱动

> 原文：<https://medium.com/codex/shardingsphere-jdbc-driver-released-a-jdbc-driver-that-requires-no-code-modifications-5464c30bcd64?source=collection_archive---------11----------------------->

# 背景

`ShardingSphereDataSourceFactory`是[Apache sharding sphere-JDBC](https://shardingsphere.apache.org/document/current/en/overview/#shardingsphere-jdbc)最基本的用户 API，用于转换用户的规则配置对象，生成`DataSource`的标准实现。

它还提供了用于`YAML`配置的`YamlShardingSphereDataSourceFactory`，以及用于 Spring 和`Spring Boot Starter`的定制名称空间。

`DataSource`是一个标准的 Java JDBC 接口。工程师可以使用它进一步创建符合 JDBC 标准的`Connection`、`Statement`、`PreparedStatement`、`ResultSet,`和其他熟悉的标准对象。它与 JDBC 接口的实现完全一致，所以当工程师使用 Apache ShardingSphere-JDBC 或使用本地 JDBC 时没有区别。此外，它还可以透明地与各种 [ORM](https://stackoverflow.com/questions/1279613/what-is-an-orm-how-does-it-work-and-how-should-i-use-one) 框架接口。

# 棘手问题

虽然标准的 JDBC 接口可以在开发过程中完全适应，但是通过 ShardingSphere API 创建一个`DataSource`可以改变工程师最初加载数据库驱动程序的方式。

尽管只有一小部分(一行)启动代码需要修改，但这实际上增加了希望平稳迁移到 ShardingSphere 的系统的额外开发成本。而对于无法掌握源代码的系统(比如外部采购系统)来说，使用 ShardingSphere 确实很困难。

很大程度上由于其设计，ShardingSphere 一直缺乏 JDBC 驱动的实现。Java 配置的 ShardingSphere-JDBC 可以实现可编程的灵活性，但 JDBC 的驱动程序接口没有为额外的配置提供太多空间。

通过鞋底`URL`和`Properties`将极大地限制切割球的配置灵活性。虽然`YAML`配置可以更好的适应司机的`URL`，可读性更强，但是属于静态配置的范畴，灵活性明显不如动态配置。

因此，ShardingSphere-JDBC 使用类似的数据库连接池策略，绕过 JDBC 标准接口的限制，直接向用户公开`DataSource`。

但是，不改一行代码几乎是牢不可破的壁垒，这是在提高 ShardingSphere-JDBC 易用性的同时最大的痛点。

# 机会

随着 [ShardingSphere-Proxy](https://shardingsphere.apache.org/document/current/en/quick-start/shardingsphere-proxy-quick-start/) 的开发(ShardingSphere 客户端)，它的两个主要生态系统特性出现了，即混合部署和 [DistSQL](https://shardingsphere.apache.org/document/5.1.0/en/concepts/distsql/) 。

ShardingSphere-JDBC 的轻量级和高性能特性使其更适合面向应用的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 操作。ShardingSphere-Proxy 的易用性和兼容性使其更适合面向数据库管理和控制的 DDL 操作。这两个客户端可以一起使用，相互补充，以提供一个新的和更完整的架构解决方案。

凭借编程和 SQL 功能，DistSQL 在灵活性和易用性之间取得了完美的平衡。在一个架构模型中，ShardingSphere-JDBC 的配置属性显著减少，使用 JDBC URL 连接到治理中心和 DistSQL 进行配置操作是最佳解决方案。

DistSQL 确保了 Java 和 YAML 所缺乏的安全性，它自然支持权限控制和 SQL 审计，以及其他高阶功能。使用 DistSQL，DBA(数据库管理员)可以非常轻松地操作和维护数据库集群。

# 履行

前提条件满足后，ShardingSphere-JDBC 版本 5.1.2 借机提供了一个 JDBC 驱动程序，它只能通过配置更改来使用，而不需要工程师修改代码。

## 驱动程序类名

`org.apache.shardingsphere.driver.ShardingSphereDriver`

## URL 配置描述

*   前缀:`jdbc:shardingsphere:`
*   配置文件:`xxx.yaml`，格式与`YAML`配置一致。
*   配置文件的加载规则:

如果没有前缀，则从绝对路径(AP)加载配置文件。

`classpath:`前缀表示从类路径加载配置文件。

# 程序

## 使用本机驱动程序

```
Class.forName("org.apache.shardingsphere.driver.ShardingSphereDriver");
String jdbcUrl = "jdbc:shardingsphere:classpath:config.yaml";

String sql = "SELECT i.* FROM t_order o JOIN t_order_item i ON o.order_id=i.order_id WHERE o.user_id=? AND o.order_id=?";
try (
        Connection conn = DriverManager.getConnection(jdbcUrl);
        PreparedStatement ps = conn.prepareStatement(sql)) {
    ps.setInt(1, 10);
    ps.setInt(2, 1000);
    try (ResultSet rs = preparedStatement.executeQuery()) {
        while(rs.next()) {
            // ...        }
    }
}
```

## 使用数据库连接池

```
String driverClassName = "org.apache.shardingsphere.driver.ShardingSphereDriver";
String jdbcUrl = "jdbc:shardingsphere:classpath:config.yaml";// take HikariCP as an example HikariDataSource dataSource = new HikariDataSource();
dataSource.setDriverClassName(driverClassName);
dataSource.setJdbcUrl(jdbcUrl);

String sql = "SELECT i.* FROM t_order o JOIN t_order_item i ON o.order_id=i.order_id WHERE o.user_id=? AND o.order_id=?";
try (
        Connection conn = dataSource.getConnection();
        PreparedStatement ps = conn.prepareStatement(sql)) {
    ps.setInt(1, 10);
    ps.setInt(2, 1000);
    try (ResultSet rs = preparedStatement.executeQuery()) {
        while(rs.next()) {
            // ...        }
    }
}
```

## 参考

*   [JDBC 司机](https://shardingsphere.apache.org/document/current/cn/user-manual/shardingsphere-jdbc/jdbc-driver/)

# 结论

JDBC 驱动程序使 ShardingSphere 比以往任何时候都更容易使用。

在不久的将来，通过直接在`URL`中提供治理中心地址，可以进一步简化 JDBC 驱动程序。Apache ShardingSphere 在多样化的分布式集群方面取得了长足的进步。

## 相关链接:

[GitHub 问题](https://github.com/apache/shardingsphere/issues)

[投稿指南](https://shardingsphere.apache.org/community/en/contribute/)

[ShardingSphere Twitter](https://twitter.com/ShardingSphere)

[切割球松弛度](https://join.slack.com/t/apacheshardingsphere/shared_invite/zt-sbdde7ie-SjDqo9~I4rYcR18bq0SYTg)

[华人社区](https://community.sphere-ex.com/)

# 作者

**张亮**

**Github:** @terrymanu

张亮， [SphereEx](https://www.sphere-ex.com/) 创始人兼 CEO，曾担任多家大型知名互联网企业的架构和数据库团队负责人。他热衷于开源，是 Apache ShardingSphere、 [ElasticJob](https://shardingsphere.apache.org/elasticjob/) 和其他知名开源项目的创始人和 PMC 主席。

他是 [Apache 软件基金会](https://www.apache.org/)成员、[微软 MVP](https://mvp.microsoft.com/) 、[腾讯云 TVP](https://cloud.tencent.com/tvp) 和[华为云 MVP](https://developer.huaweicloud.com/mvp) 成员，在架构和数据库领域拥有超过 10 年的经验。他崇尚优雅的代码，在分布式数据库技术和学术研究方面取得了很大的成就。他曾在数十个主要的国内外工业和技术峰会上担任制片人和演讲人，包括 ApacheCon、QCon、AWS summit、DTCC、SACC 和 DTC。此外，他还出版了《未来架构:从服务到原生云》(Future Architecture:From Service to Cloud Native)(T9)一书，以及在今年的 ICDE(数据库领域的顶级会议)上发表的论文《Apache ShardingSphere:一个整体的可插拔的数据分片平台》(T11)。