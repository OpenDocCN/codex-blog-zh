# 仅对函数索引进行索引扫描

> 原文：<https://medium.com/codex/index-only-scan-on-functional-indexes-943eb2828fef?source=collection_archive---------10----------------------->

![](img/67421fc0433ba1e8a281336691cda5cd.png)

詹姆斯·哈里逊在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在过去的一篇[帖子](https://franckpachot.medium.com/index-only-access-with-oracle-mysql-postgresql-and-microsoft-sql-server-302383103998)中，我详细介绍了最常见的 RDBMS 如何避免索引访问中代价最大的操作，即使用仅索引扫描查找表中分散的行。我提到了 PostgreSQL 的局限性，其中行的 ACID 可见性不存储在索引中，因此仅索引扫描仅对新清空的表有意义。还有另一个限制，很容易通过索引或表中的少量冗余存储来解决。

这是我目前没有索引的表:

```
postgres=# set enable_bitmapscan=false;
SET
postgres=# create table demo (id bigint, username text);
CREATE TABLE
postgres=# insert into demo select n,'Number'||to_hex(n) from generate_series(1,1000) n;
INSERT 0 1000
postgres=# vacuum demo;
VACUUM
postgres=# select * from demo where username='Number42';
 id | username
----+----------
 66 | Number42
(1 row)
```

注意，我已经禁用了位图扫描来简化测试用例。目标是显示索引扫描与仅索引扫描。

我的目标是使用应用于列的函数进行搜索，仅返回该值(应用函数):

```
postgres=# explain analyze select * from demo 
           where upper(username)='NUMBER42'; QUERY PLAN
--------------------------------------------------------------------
 Seq Scan on demo  (cost=0.00..22.00 rows=5 width=17) (actual time=0.057..0.657 rows=1 loops=1)
   Filter: (upper(username) = 'NUMBER42'::text)
   Rows Removed by Filter: 999
```

我可以添加一个基于函数的索引来快速访问这些行:

```
postgres=# create index demo_upper on demo( (upper(username)) );
CREATE INDEX
postgres=# explain analyze select upper(username) from demo 
           where upper(username)='NUMBER42'; QUERY PLAN
--------------------------------------------------------------------
 Index Scan using demo_upper on demo  (cost=0.28..20.38 rows=5 width=32) (actual time=0.025..0.026 rows=1 loops=1)
   Index Cond: (upper(username) = 'NUMBER42'::text)
```

这还不错，但我们可以做得更好。为什么不使用索引只扫描这里，因为我需要的所有信息(upper(username))都在索引和可见性图中(自从上次清空以来，我没有得到任何更改)？问题是查询规划器只需获取表达式，看到它有一个索引，知道我将选择“username”列并对其应用一个函数。然后，它认为它需要“username”列，而没有意识到它已经具有应用了函数的值。

# 覆盖指数

一个简单的解决方法是:将该列添加到索引中。这可以通过覆盖指数来实现:

```
postgres=# create index demo_upper_covering on demo( (upper(username))) include (username )
CREATE INDEX
postgres=# analyze demo;
ANALYZEpostgres=# explain analyze select upper(username) from demo 
           where upper(username)='NUMBER42'; QUERY PLAN
--------------------------------------------------------------------
 Index Only Scan using demo_upper_covering on demo  (cost=0.28..4.38 rows=5 width=32) (actual time=0.027..0.029 rows=1 loops=1)
   Index Cond: ((upper(username)) = 'NUMBER42'::text)
   Heap Fetches: 0
```

这是我唯一的索引扫描。但是，索引稍微大了一点，这意味着共享缓冲区和文件系统缓存中的页面更少了。

# 计算列

从 PG12 开始的另一种可能性是在表中添加 this (upper(username)。最大的优点是查询将使用这个生成的列，而不必每次都编码表达式，没有 where 子句和 select 子句不一致的风险。我们会将它编入索引，这样它在索引中的存在就没有疑问了。

```
postgres=# alter table demo add column upper_username text generated always as (upper(username)) stored;
ALTER TABLE
postgres=# create index demo_upper_stored on demo( upper_username );
CREATE INDEX
postgres=# analyze demo;
ANALYZEpostgres=# explain analyze select upper_username from demo
           where upper_username='NUMBER42'; QUERY PLAN
--------------------------------------------------------------------
 Index Only Scan using demo_upper_stored on demo  (cost=0.28..8.29 rows=1 width=9) (actual time=0.022..0.023 rows=1 loops=1)
   Index Cond: (upper_username = 'NUMBER42'::text)
   Heap Fetches: 1
```

这就是:对于开发人员来说易于查询，对于 DBA 来说易于索引，对于查询规划人员来说易于优化。这里仍然有一些冗余存储，但是在表中这是一个较小的问题(因为我们的目标不是在这里读表……)

# 版本和替代方案

目前(PG13)仅支持存储生成的列。如果有一天允许虚拟的，可能会更好。关于兼容 PostgreSQL 的数据库，AWS Aurora 支持所有带有预配版本的数据库。无服务器版本与 PG10 兼容，支持函数索引，但不支持生成的列。YugabyteDB 目前与 PG11 兼容，因此解决方案是功能索引。CockroachDB 没有基于函数的索引，但是生成了列(存储的和虚拟的，但是只有存储列上的索引不需要转到主列)

db <>拨弄这个:

 [## Postgres 13 | db 小提琴

### 使用 db fiddle，即表示您同意许可 Creative Commons CC0 提交的所有内容。创建了 2137781 把小提琴(22448…

dbfiddle.uk](https://dbfiddle.uk/?rdbms=postgres_13&fiddle=7a4dc212f126a17e4014c41dbcd78a74) 

OLTP 的最佳性能优化是，对于读取许多行的关键用例，避免访问表的随机读取。SSD 减少了随机读取延迟，但现在对于分布式数据库，我们希望避免跨节点延迟。RDBMS 有许多存储冗余数据的可能性，以保持将被一起查询的内容的聚集。而且，由于 SQL(和 Edgar F. Codd 关系规则 8 和 9)，这只是 DDL 而不需要改变代码中的 DML，从而在不牺牲灵活性的情况下获得更好的可伸缩性。基于函数的索引和覆盖索引是它的关键特性，允许只扫描索引，即使是二级索引。