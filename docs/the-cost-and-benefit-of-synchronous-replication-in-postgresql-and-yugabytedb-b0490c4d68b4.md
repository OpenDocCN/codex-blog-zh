# PostgreSQL 和 YugabyteDB 中同步复制的成本和收益

> 原文：<https://medium.com/codex/the-cost-and-benefit-of-synchronous-replication-in-postgresql-and-yugabytedb-b0490c4d68b4?source=collection_archive---------6----------------------->

![](img/087add9f3cbad11a2d115a9fcce5f772.png)

穆罕默德·拉赫马尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我见过一个人比较 YugabyteDB 和 PostgreSQL，当在一个会话中运行一个简单的测试时，对不同的吞吐量感到惊讶。分布式数据库的目的是向外扩展。当在单个节点上运行而不需要无数据丢失的高可用性时(这是一个同义反复)，一个整体数据库将总是以较低的延迟执行。因为分布式数据库旨在通过 RPC(远程过程调用)而不是本地写入来确保持久性(ACID 中的 D)。

下面是一个简单的工作负载:

```
drop table if exists demo;
create table demo(
 i int primary key,
 t timestamp default clock_timestamp()
);\timing ondo $$ 
 begin
  truncate demo; 
  for i in 1..1e4 loop
   insert into demo(i) values(i);
   commit; 
  end loop; 
 end; 
$$;select
 count(*)/extract(epoch from max(t)-min(t)) "rows/s",
 count(*),max(t)-min(t) "duration"
 from demo;
```

# YugabyteDB

以下是当前生产(稳定)版本中的运行情况，RF=3，所有节点都在同一虚拟机上(对于此测试，为了独立于网络延迟，您不会在生产中这样做):

```
[postgres@yb0 ~]$ psql -p 5433yugabyte=# select version();
                                                  version
------------------------------------------------------------------------------------------------------------
 PostgreSQL 11.2-YB-2.6.1.0-b0 on x86_64-pc-linux-gnu, compiled by gcc (Homebrew gcc 5.5.0_4) 5.5.0, 64-bityugabyte=# do $$ begin truncate demo; for i in 1..1e4 loop insert into demo(i) values(i); commit; end loop; end; $$;
DO
Time: 37130.036 ms (00:37.130)yugabyte=# select count(*)/extract(epoch from max(t)-min(t)) "rows/s",count(*),max(t)-min(t) "duration" from demo; rows/s      | count |    duration
------------------+-------+-----------------
 270.115078207229 | 10000 | 00:00:37.021258
```

数字本身并不重要。这是一个在一台虚拟机上的实验室，但是我将在同一台机器上运行所有的东西来比较吞吐量。

# PostgreSQL 无 HA

我正在同一台服务器上启动 PostgreSQL:

```
/usr/pgsql-13/bin/initdb -D /var/tmp/pgdata
echo "port=5432" >> /var/tmp/pgdata/postgresql.conf
/usr/pgsql-13/bin/pg_ctl -D /var/tmp/pgdata -l logfile start
```

并运行相同的:

```
[postgres@yb0 ~]$ psql -p 5432postgres=# select version(); version
-------------------------------------------------------------------------------------------------------------
 PostgreSQL 13.4 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 8.4.1 20200928 (Red Hat 8.4.1-1), 64-bitpostgres=# do $$ begin truncate demo; for i in 1..1e4 loop insert into demo(i) values(i); commit; end loop; end; $$;
DO
Time: 5533.086 ms (00:05.533)postgres=# select count(*)/extract(epoch from max(t)-min(t)) "rows/s",count(*),max(t)-min(t) "duration" from demo; rows/s       | count |    duration
-------------------------+-------+-----------------
 1809.0359900474075 | 10000 | 00:00:05.527806
```

这就是为什么你认为 PostgreSQL 更快。是的，这里的每秒事务数是 1:7。

但是我们是在比较苹果和橘子的弹性。YugabyteDB 在复制因子 RF=3 的情况下运行，因此每次写入都会传播到 3 个副本中的 2 个。在 RF=3 的 Yugabyte 集群中，您可以删除一个节点，并且:

*   2/3 的读取和写入继续运行，就像什么都没发生一样。多亏了将桌子分割成平板电脑。
*   1/3 的读取和写入，即那些其领导者在失效节点上的读取和写入，必须等待几秒钟才能让幸存节点上的一个跟随者被选举为新的领导者(Raft 协议)

一切都在继续，因为我们有法定人数。一切都是一致的。并且没有丢失任何提交的事务。唯一的后果是，在第一个节点恢复或添加新节点之前，丢失第二个节点将会停止数据库。仍然没有数据丢失。但是根据定义，RF=3 只能容忍一个节点停机。

这种保护涉及远程过程调用。让我们看看 PostgreSQL 在更高的可用性下会如何表现

# 带备用数据库的 PostgreSQL

我将向我的 PostgreSQL 集群添加两个备用数据库:

```
/usr/pgsql-13/bin/pg_basebackup -p 5432 -D /var/tmp/pgsby1 -R --slot=sby1 -C
echo "port=5441" >> /var/tmp/pgsby1/postgresql.conf
touch /var/tmp/pgsby1/recovery.signal
/usr/pgsql-13/bin/pg_ctl -D /var/tmp/pgsby1 -l logfile start/usr/pgsql-13/bin/pg_basebackup -p 5432 -D /var/tmp/pgsby2 -R --slot=sby2 -C
echo "port=5442" >> /var/tmp/pgsby2/postgresql.conf
touch /var/tmp/pgsby2/recovery.signal
/usr/pgsql-13/bin/pg_ctl -D /var/tmp/pgsby2 -l logfile start
```

从主视图中可以看到它们:

```
[postgres@yb0 ~]$ psql -p 5432 -c "select * from pg_replication_slots" slot_name | plugin | slot_type | datoid | database | temporary | active | active_pid | xmin | catalog_xmin | restart_lsn | confirmed_flush_lsn | wal_status | safe_wal_size
----------------+--------+-----------+--------+----------+-----------+--------+------------+------+--------------+-------------+---------------------+------------+---------------
 sby1      |        | physical  |        |          | f         | t      |     298963 |      |              | 0/5000060   |                     | reserved   |
 sby2      |        | physical  |        |          | f         | t      |     299054 |      |              | 0/5000060   |                     | reserved   |
(2 rows)[postgres@yb0 ~]$ psql -p 5432 -c "select * from pg_stat_replication" pid   | usesysid | usename  | application_name | client_addr | client_hostname | client_port |         backend_start         | backend_xmin |   state   | sent_lsn  | write_lsn | flush_lsn | replay_lsn | write_lag | flush_lag | replay_la
g | sync_priority | sync_state |          reply_time
-------------+----------+----------+------------------+-------------+-----------------+-------------+-------------------------------+--------------+-----------+-----------+-----------+-----------+------------+-----------+-----------+----------
--+---------------+------------+-------------------------------
 298963 |       10 | postgres | walreceiver      |             |                 |          -1 | 2021-09-28 13:03:45.30363+00  |              | streaming | 0/5000060 | 0/5000060 | 0/5000060 | 0/5000060  |           |           |
  |             0 | async      | 2021-09-28 13:04:23.662693+00
 299054 |       10 | postgres | walreceiver      |             |                 |          -1 | 2021-09-28 13:03:48.668362+00 |              | streaming | 0/5000060 | 0/5000060 | 0/5000060 | 0/5000060  |           |           |
  |             0 | async      | 2021-09-28 13:04:23.663322+00
(2 rows)
```

这是两个异步备用服务器，接收流式传输的数据。

我运行同样的小型工作负载:

```
postgres=# do $$ begin truncate demo; for i in 1..1e4 loop insert into demo(i) values(i); commit; end loop; end; $$;
select count(*)/extract(epoch from max(t)-min(t)) "rows/s",count(*),max(t)-min(t) "duration" from demo;
DO
Time: 6437.456 ms (00:06.437)postgres=# select count(*)/extract(epoch from max(t)-min(t)) "rows/s",count(*),max(t)-min(t) "duration" from demo; rows/s       | count |    duration
-------------------------+-------+-----------------
 1554.5772428235664 | 10000 | 00:00:06.432617
```

这还是挺快的。但是这就是高可用性吗？一点也不。是的，在主数据库完全失败的情况下，我不需要恢复备份，并且可以故障转移到其中一个备用数据库。但是:

*   我将丢失一些提交的事务，因为我处于异步复制状态。恢复点目标是 RPO>0
*   由于上述原因，这不能自动化。在打开备用站点之前，您需要一个人的决策来评估数据丢失的风险，以及恢复发生故障的主站点的可能性，至少恢复带有最新事务的 WAL。在实践中，人为决策意味着以小时为单位的恢复时间目标:RTO>0

这是无法与 YugabyteDB 复制相比的，在 YugabyteDB 复制中，所有操作都在几秒钟内自动完成，没有数据丢失。

# 带同步备用的 PostgreSQL

我们可以通过同步复制降低 RPO:

```
echo "synchronous_standby_names = '*'" >> /var/tmp/pgdata/postgresql.conf
/usr/pgsql-13/bin/pg_ctl -D /var/tmp/pgdata reload
```

这种配置确保在我们向用户返回成功的提交之前，一个备用数据库收到了覆盖事务的 WAL:

```
[postgres@yb0 ~]$ psql -p 5432 -c "select * from pg_stat_replication" pid   | usesysid | usename  | application_name | client_addr | client_hostname | client_port |         backend_start         | backend_xmin |   state   | sent_lsn  | write_lsn | flush_lsn | replay_lsn | write_lag | flush_lag | replay_la
g | sync_priority | sync_state |          reply_time
-------------+----------+----------+------------------+-------------+-----------------+-------------+-------------------------------+--------------+-----------+-----------+-----------+-----------+------------+-----------+-----------+----------
--+---------------+------------+------------------------------- 298963 |       10 | postgres | walreceiver      |             |                 |          -1 | 2021-09-28 13:03:45.30363+00  |              | streaming | 0/538E3F0 | 0/538E3F0 | 0/538E3F0 | 0/538E3F0  |           |           |
  |             1 | sync       | 2021-09-28 13:14:12.307231+00
 299054 |       10 | postgres | walreceiver      |             |                 |          -1 | 2021-09-28 13:03:48.668362+00 |              | streaming | 0/538E3F0 | 0/538E3F0 | 0/538E3F0 | 0/538E3F0  |           |           |
  |             1 | potential  | 2021-09-28 13:14:12.307294+00
(
```

现在，如果我再次运行我的小工作量:

```
postgres=# do $$ begin truncate demo; for i in 1..1e4 loop insert into demo(i) values(i); commit; end loop; end; $$;
DO
Time: 13613.487 ms (00:13.613)postgres=# select count(*)/extract(epoch from max(t)-min(t)) "rows/s",count(*),max(t)-min(t) "duration" from demo; rows/s      | count |    duration
-----------------------+-------+-----------------
 734.861683966413 | 10000 | 00:00:13.608003
```

吞吐量被除以 2。

我们这里的可用性高吗？这种同步配置需要复杂的监控和管理。因为，即使在同步中，WAL 的持久化也不会同时发生:首先写入并(fsync'd)然后发送(并确认)到备用数据库，然后向用户返回“提交成功”。这里没有两阶段提交。在失败的情况下，这与我们在分布式数据库中发现的一致协议非常不同。PostgreSQL 数据库通常与 ASYNC 一起使用，这是一个非常好的 DR(灾难恢复)解决方案，在手动故障转移后数据丢失最少。同步复制是可能的，但不如分布式数据库那样高的可用性。

数字在这里并不重要。它们将依赖于您的机器和网络。分布式数据库可以在多 AZ 集群甚至多区域中同步。关键是单个会话的吞吐量较低。但是，因为所有节点都是活动的，所以当在所有节点上对多个会话进行负载平衡时，这种情况会向外扩展。对于只读 PostgreSQL 备用数据库，您无法做到这一点。

# YugabyteDB 横向扩展

我将在我的表中为工号添加一个“j”列:

```
drop table if exists demo;
create table demo(
 j int, i int,
 t timestamp default clock_timestamp(),
 primary key(j,i)
);
```

并运行 3 个并行作业进行相同的插入:

```
for i in {0..2} ; do
psql -h yb$i -p 5433 -c 'do $$ begin for i in 1..1e4 loop insert into demo(j,i) values('$i',i); commit; end loop; end; $$ ; ' &
done ; wait
```

结果如下:

```
postgres=# select j, count(*)/extract(epoch from max(t)-min(t)) "rows/s",count(*),max(t)-min(t) "duration"
           from demo group by j; j |      rows/s      | count |    duration
--------+------------------+-------+-----------------
 0 | 181.329577731828 | 10000 | 00:00:55.148201
 2 | 180.088033513951 | 10000 | 00:00:55.528398
 1 | 188.044672569376 | 10000 | 00:00:53.178853
(3 rows)
```

在我的 3 个并发会话中，我每秒插入了 550 个事务。再说一次，这是一个小实验室。虽然单会话短事务由于提交的分布式特性而具有有限的速率，但是它可以扩展到许多节点。如果您停留在一个虚拟机上，而没有同步复制到另一个站点，PostgreSQL 会更快。分布式数据库显示其全部能力的地方是当您添加节点时，为了高可用性和负载平衡，而没有增加复杂性，因为所有的分布式协议已经存在。

在这个简短的测试中，还有一些你看不到的东西。PostgreSQL 不能无限期地支持这些插入。共享缓冲区已满，将出现检查点，文件系统缓存将同步到磁盘。最重要的是:在某些时候，您需要在事务 id 回绕之前清空表，否则数据库会挂起。在 PostgreSQL 中，insert 的前几分钟是非常乐观的，这对于短时间的活动高峰来说是没有问题的。

注意，我以前写过类似的关于 RDS PostgreSQL 和 Aurora 的文章。即使两者都无法横向扩展写入，Aurora 中的高可用性也依赖于远程 WAL 同步来实现更好的高可用性(RPO=0 / RTO 只需几分钟)。

要考虑的参数太多？不要惊慌。因为 YugabyteDB 与 PostgreSQL 有相同的 API 它使用相同的 SQL 和 PL/pgSQL 层以及类似的开源许可证——所以您不会被锁定在最初的决定中。你可以从 PostgreSQL 开始，用 YugabyteDB 伸缩，反之亦然。