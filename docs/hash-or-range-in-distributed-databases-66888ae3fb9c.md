# 分布式数据库中的散列或范围

> 原文：<https://medium.com/codex/hash-or-range-in-distributed-databases-66888ae3fb9c?source=collection_archive---------17----------------------->

这是一个适用于任何数据库的一般思想:您希望将被一起检索的数据放在一起。在通常为一个用例构建的 NoSQL 数据库中，这很容易:您有一个用例，您有一个数据存储(在 MongoDB 中称为集合，或者在 DynamoDB 中不恰当地称为“表”),并且有一个分区方案:散列。在关系数据库中，这是不同的:数据被存储以供多个用例访问。因为不同的用户可能从不同的业务角度进行导航或聚合。您有多个表(规范化以避免更新异常)、多个表和索引分区方案。基本上，当您想要将数据放在一起时，有两种结构:

*   HASH:对键或索引项应用散列函数，相同的散列值将进入相同的分区。
*   RANGE:对键进行排序，以便按顺序存储值，或者，至少，当它们有封闭值时，存储在同一个分区中。

对于我的例子，在 YugabyteDB 上，我将加载一个复仇者表:

```
python -c "from sqlalchemy import create_engine;import pandas;import io;import urllib.request;
pandas.read_csv(io.StringIO(urllib.request.urlopen('https://github.com/fivethirtyeight/data/blob/master/avengers/avengers.csv?raw=True').read().decode('utf-8', errors='ignore'))).to_sql('avengers',
create_engine('postgresql+psycopg2://franck:yugabyte@yb1.pachot.net:5433/yugabyte'), if_exists='replace', method='multi')"
```

这是一个简短的 python 代码，从[https://fivethirtyeight.com/](https://fivethirtyeight.com/)加载 avengers.csv，这里的连接是我的数据库，我保持公开开放，以防你想测试。YugabyteDB 与 PostgreSQL 完全兼容，因此您可以使用任何 postgres 驱动程序或工具。

但是，我将在地理分布的集群上进行测试，以便了解从多个节点读取时的延迟:

![](img/f34862dfcbac2472121e441684949972.png)

我在 YugabyteDB 表中有 173 个复仇者和一个索引:

```
yugabyte=> select count(*) from avengers; count
-------
   173yugabyte=> select indexdef from pg_indexes
where  ( schemaname , tablename  )
     = ( 'public'   , 'avengers' )
;
                                 indexdef
--------------------------------------------------------------------------
 CREATE INDEX ix_avengers_index ON public.avengers USING lsm (index HASH)
```

已经在主键上创建了索引。实际上，在 YugabyteDB 中，所有的表都存储在一个索引结构中(如 MySQL 中的 InnoDB 表、SQL Server 中的聚集索引或 Oracle 中的索引组织表)，这个索引结构不是 B *树，而是一个 [LSM —日志结构合并](https://blog.yugabyte.com/a-busy-developers-guide-to-database-storage-engines-the-basics/)。这里很重要的一点是，这个默认创建的索引是基于散列的。当来自其他分布式数据库时，这可能是您所知道的唯一的分片或分区方案。但是在 YugabyteDB 中，您可以选择 HASH，HASH 只是默认设置。

我们来看看有哪些优化的访问路径。基本上我们会看到:

*   **Seq Scan** 从所有平板电脑读取所有行，这意味着从所有节点，然后过滤它们。只有当您需要整个表中的许多行时，这才是最佳选择。
*   **步进扫描**，仅访问特定的数位板和数位板上的特定范围，最适合读取表格行的特定部分。

```
yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers; QUERY PLAN
-----------------------------------------------------------------------------------------------------------------------
 Seq Scan on public.avengers  (cost=0.00..100.00 rows=1000 width=152) (actual time=120.770..481.221 rows=173 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
```

如果没有任何 where 子句，我们当然需要读取所有的表行，Seq Scan 是正确的访问路径。

# 散列索引

让我们使用关键字上的谓词进行过滤:

```
yugabyte=> select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where index=73; index |      Name/Alias       | Appearances | Gender | Year | Death1 |                                                                                                       Notes------------+-----------------------+-------------+--------+------+--------+-----------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------
    73 | Peter Benjamin Parker |        4333 | MALE   | 1990 | YES    | Since joining the New Avengers: First death Killed by Morlun. Ressurected in a brand new body. Died in Amazing Spider-
Man #700\. Eventually mainfested in his body that Octavious stole and then took over again.
(1 row)yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where index=73;
                                                              QUERY PLAN
-------------------------------------------------------------------------------------------------------------------------------------------
 Index Scan using avengers_pkey on public.avengers  (cost=0.00..4.11 rows=1 width=152) (actual time=120.434..120.437 rows=1 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Index Cond: (avengers.index = 73)
```

在这里，索引扫描通过“索引条件”知道要访问表的哪一部分。这是一个等式谓词，在散列键上，值被散列，可以到达正确的分区，以及该分区中的正确位置。
让我们更进一步:

```
yugabyte=> explain (analyze, verbose)
           select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where index in (73,6);
                                                              QUERY PLAN
-------------------------------------------------------------------------------------------------------------------------------------------
 Index Scan using avengers_pkey on public.avengers  (cost=0.00..4.11 rows=1 width=152) (actual time=841.051..841.057 rows=2 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Index Cond: (avengers.index = ANY ('{73,6}'::bigint[]))
```

对于许多值，索引扫描仍然是可能的:每个值在其分区中被访问。

```
yugabyte=> select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes"
from avengers where index<5; index |         Name/Alias          | Appearances | Gender | Year | Death1 |                                                                                      Notes------------+-----------------------------+-------------+--------+------+--------+-----------------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------
     2 | Anthony Edward "Tony" Stark |        3068 | MALE   | 1963 | YES    | Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'
This set up young Tony. Franklin Richards later brought him back
     0 | Henry Jonathan "Hank" Pym   |        1269 | MALE   | 1963 | YES    | Merged with Ultron in Rage of Ultron Vol. 1\. A funeral was held.
     3 | Robert Bruce Banner         |        2089 | MALE   | 1963 | YES    | Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
     1 | Janet van Dyne              |        1165 | FEMALE | 1963 | YES    | Dies in Secret Invasion V1:I8\. Actually was sent tto Microverse later recovered
     4 | Thor Odinson                |        2402 | MALE   | 1963 | YES    | Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not y
et returned
(5 rows)yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where index<5; QUERY PLAN
---------------------------------------------------------------------------------------------------------------------
 Seq Scan on public.avengers  (cost=0.00..102.50 rows=1000 width=152) (actual time=120.801..481.287 rows=5 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Filter: (avengers.index < 5)
   Rows Removed by Filter: 168
```

对于不等式谓词，我们不能使用散列索引，因为散列函数不保持顺序。在这种情况下，Seq 扫描是唯一的解决方案，从所有平板电脑中读取所有行，然后过滤它们。这不是最佳的访问路径，对于关键用例应该避免。

当您定义主键时，您可能不知道所有的用例。但是你知道你的数据。在这种情况下,“索引”主键是一个数字，只是为了获得一个小的标识符，但是该值没有业务意义，也没有像比较和排序这样的算术目的。没有理由在某个范围内对它进行查询或排序，而且它可能不需要范围键。当您知道您不需要范围扫描时，哈希键会更加灵活，因为通过一致的哈希分片，您可以在节点之间实现最佳分布，并在重新平衡、添加或删除时保持这种分布。

# 距离指标

有些列是有顺序的，并且您肯定希望在某个范围内进行查询。让我们索引保存复仇者联盟角色在漫威漫画书中出现次数的列:

```
yugabyte=> create index i0 on avengers("Appearances");
CREATE INDEXyugabyte=> select indexdef from pg_indexes 
where  ( schemaname , tablename  ) = ( 'public'   , 'avengers' ) ; indexdef
-------------------------------------------------------------------------------
 CREATE INDEX ix_avengers_index ON public.avengers USING lsm (index HASH)
 CREATE INDEX i0 ON public.avengers USING lsm ("Appearances" HASH)
```

没有提到任何 ASC 或 DESC，它默认为哈希。

```
yugabyte=> select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Appearances"<5 order by "Appearances"; index |  Name/Alias   | Appearances | Gender | Year | Death1 |                                        Notes
------------+---------------+-------------+--------+------+--------+-------------------------------------------------------------------------------------
   125 | Fiona         |           2 | FEMALE | 1900 | NO     |
    39 | Moira Brandon |           2 | FEMALE | 1993 | YES    | Died in her second appearance earns honorary Avengers status doing so. Stays dead.
    68 | Doug Taggert  |           3 | MALE   | 2005 | YES    | Accidently killed by Zaran
    65 | Gene Lorrene  |           4 | MALE   | 2005 | NO     |
(4 rows)yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Appearances"<5 order by "Appearances"; QUERY PLAN
---------------------------------------------------------------------------------------------------------------------------
 Sort  (cost=152.33..154.83 rows=1000 width=152) (actual time=481.279..481.280 rows=4 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Sort Key: avengers."Appearances"
   Sort Method: quicksort  Memory: 25kB
   ->  Seq Scan on public.avengers  (cost=0.00..102.50 rows=1000 width=152) (actual time=120.797..481.268 rows=4 loops=1)
         Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
         Filter: (avengers."Appearances" < 5)
         Rows Removed by Filter: 169
```

在查询外貌少于 5 的复仇者时，这里没有使用我的哈希索引。只有当条件中有离散值时，才可以使用散列法:

```
yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Appearances" in (1,2,3,4,5) order by "Appearances";
 QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------------
 Sort  (cost=5.43..5.45 rows=10 width=152) (actual time=480.476..480.477 rows=4 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Sort Key: avengers."Appearances"
   Sort Method: quicksort  Memory: 25kB
   ->  Index Scan using i0 on public.avengers  (cost=0.00..5.26 rows=10 width=152) (actual time=359.614..480.466 rows=4 loops=1)
         Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
         Index Cond: (avengers."Appearances" = ANY ('{1,2,3,4,5}'::bigint[]))
```

这个用处不大。对于这样的列，如果某个范围或顺序有意义，我们需要定义一个范围索引，按升序或降序排列:

```
yugabyte=> drop index i0;
DROP INDEX
yugabyte=> create index i0 on avengers("Appearances" asc);
CREATE INDEXyugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Appearances"<5 order by "Appearances"; QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------
 Index Scan using i0 on public.avengers  (cost=0.00..5.22 rows=10 width=152) (actual time=599.444..599.452 rows=4 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Index Cond: (avengers."Appearances" < 5)
```

这里已经扫描了索引，即使扫描顺序(这里从 5 开始递减)与索引顺序(ASC)相反。没有对输出进行额外的过滤，因为我们只读取了所需的行。没有排序操作，因为我们知道它们是按照我们想要的顺序来的。

```
yugabyte=> select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Appearances">2000 order by "Appearances"; index |         Name/Alias          | Appearances | Gender | Year | Death1 |                                                                                                       Notes------------+-----------------------------+-------------+--------+------+--------+-----------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------------
     3 | Robert Bruce Banner         |        2089 | MALE   | 1963 | YES    | Dies in Ghosts of the Future arc. However "he had actually used a hidden Pantheon base to survive"
    57 | Reed Richards               |        2125 | MALE   | 1989 | NO     |
    40 | Benjamin Jacob Grimm        |        2305 | MALE   | 1986 | YES    | Once killed during a battle with Doctor Doom.' Brought back by the FF when they literally went to Heaven to get
him.
     4 | Thor Odinson                |        2402 | MALE   | 1963 | YES    | Dies in Fear Itself brought back because that's kind of the whole point. Second death in Time Runs Out has not y
et returned
     2 | Anthony Edward "Tony" Stark |        3068 | MALE   | 1963 | YES    | Death: "Later while under the influence of Immortus Stark committed a number of horrible acts and was killed.'
This set up young Tony. Franklin Richards later brought him back
    92 | James "Logan" Howlett       |        3130 | MALE   | 2005 | YES    | Died in Death_of_Wolverine_Vol_1_4\. Has not yet returned
     6 | Steven Rogers               |        3458 | MALE   | 1964 | YES    | Dies at the end of Civil War. Later comes back.
    73 | Peter Benjamin Parker       |        4333 | MALE   | 1990 | YES    | Since joining the New Avengers: First death Killed by Morlun. Ressurected in a brand new body. Died in Amazing S
pider-Man #700\. Eventually mainfested in his body that Octavious stole and then took over again.
(8 rows)yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Appearances">2000 order by "Appearances"; QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------
 Index Scan using i0 on public.avengers  (cost=0.00..5.22 rows=10 width=152) (actual time=239.388..239.401 rows=8 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Index Cond: (avengers."Appearances" > 2000)
```

当然，按照升序阅读时也是如此。

因此，对于顺序有意义的数值型数据类型，您需要定义 ASC 或 DESC。如果顺序有意义，字符串列也是如此。让我们索引复仇者的名字，首先用一个哈希:

```
yugabyte=> create index i1 on avengers("Name/Alias");
CREATE INDEXyugabyte=> select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Name/Alias"='Peter Benjamin Parker'; index |      Name/Alias       | Appearances | Gender | Year | Death1 |                                                                                                       Notes------------+-----------------------+-------------+--------+------+--------+-----------------------------------------------------------------------------------------------------------------------
-------------------------------------------------------------------------------------------------
    73 | Peter Benjamin Parker |        4333 | MALE   | 1990 | YES    | Since joining the New Avengers: First death Killed by Morlun. Ressurected in a brand new body. Died in Amazing Spider-
Man #700\. Eventually mainfested in his body that Octavious stole and then took over again.
(1 row)yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Name/Alias"='Peter Benjamin Parker'; QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------
 Index Scan using i1 on public.avengers  (cost=0.00..5.22 rows=10 width=152) (actual time=239.840..239.844 rows=1 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Index Cond: (avengers."Name/Alias" = 'Peter Benjamin Parker'::text)
```

对于全称上的谓词，使用散列索引。但是假设你不知道蜘蛛侠的中间名:

```
yugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Name/Alias" like 'Peter% Parker'; QUERY PLAN
---------------------------------------------------------------------------------------------------------------------
 Seq Scan on public.avengers  (cost=0.00..102.50 rows=1000 width=152) (actual time=479.191..480.680 rows=1 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Filter: (avengers."Name/Alias" ~~ 'Peter% Parker'::text)
   Rows Removed by Filter: 172
```

使用`LIKE 'Peter% Parker'`模式，我们必须扫描所有的行。我们能做得更好吗？让我们创建一个范围索引:

```
yugabyte=> create index i2 on avengers("Name/Alias" asc);
CREATE INDEXyugabyte=> explain (analyze, verbose)
select index,"Name/Alias","Appearances","Gender","Year","Death1","Notes" 
from avengers where "Name/Alias" like 'Peter% Parker'; QUERY PLAN
----------------------------------------------------------------------------------------------------------------------------
 Index Scan using i2 on public.avengers  (cost=0.00..5.25 rows=10 width=152) (actual time=480.660..480.665 rows=1 loops=1)
   Output: index, "Name/Alias", "Appearances", "Gender", "Year", "Death1", "Notes"
   Index Cond: ((avengers."Name/Alias" >= 'Peter'::text) AND (avengers."Name/Alias" < 'Petes'::text))
   Filter: (avengers."Name/Alias" ~~ 'Peter% Parker'::text)
```

这里，查询规划器知道以“Peter”开头的名字都在同一位置，这是对连续条目的索引访问。这就是关系数据库比 NoSQL 更强大的地方:您有多条访问路径和一个查询规划器来找到最佳路径。

注意，我在这里创建了两个索引，但是索引越多，插入、删除或更新索引列的开销就越大。这里，散列索引没有意义，因为范围索引也可以服务于等式谓词

```
yugabyte=> drop index i1;
DROP INDEX
```

还有更多要说的，但我想保持简单。YugabyteDB 文档涵盖了它。数据建模并不复杂。在 SQL 数据库上，由于 DDL 语言，您可以灵活地更改模型，并且与视图保持逻辑独立。但是对于散列或范围键，因为它也定义了数据的物理位置，所以最好提前考虑一下。如果您知道所有的关键用例，那么看看它们是如何访问数据的。如果不是都计划好了，也不要慌。思考数据的商业意义。有些数字有顺序(你的工资、生日、工作时间……)，有些没有顺序(客户 id、代理键……)。有些字符串有顺序(您的姓名、产品名称、学校等级……)，有些没有顺序(您的汽车牌照号码、描述……)

您还可以索引多个列，其中第一列被散列以对其进行分区，第二列被排序以按范围扫描并排序。您还可以在表组中将表放在一起。我们将在以后的帖子中看到这一点。