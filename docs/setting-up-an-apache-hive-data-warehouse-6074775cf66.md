# 设置 Apache Hive 数据仓库

> 原文：<https://medium.com/codex/setting-up-an-apache-hive-data-warehouse-6074775cf66?source=collection_archive---------10----------------------->

Apache Hive 数据仓库软件有助于在 Hadoop 分布式文件系统中使用 SQL 读取、写入和管理大型数据集。在这篇文章中，我将在 Apache Hadoop 3.3.0 上设置 Apache Hive 3.1.2。Hive 需要一个现成的 Hadoop 框架。我强烈建议你使用我已经发表的名为[运行多节点 Hadoop 集群](/codex/running-a-multi-node-hadoop-cluster-257068e5f276)的帖子来设置你的 Hadoop 框架。

[](/codex/running-a-multi-node-hadoop-cluster-257068e5f276) [## 运行多节点 Hadoop 集群

### Apache Hadoop 软件库是一个框架，允许跨…分布式处理大型数据集

medium.com](/codex/running-a-multi-node-hadoop-cluster-257068e5f276) ![](img/c24cc0955217ac18a5c00639865e7ca7.png)

[阿帕奇蜂巢](https://hive.apache.org/)

Apache Hive 使用数据库来存储其元数据。我将使用一个 [Postgres](https://www.postgresql.org/) 数据库作为外部 metastore，因此我们应该首先安装一个 Postgres 数据库。我可能会使用一些 CentOS (RHEL)命令。我们将通过以下步骤来运行 Apache Hive。

1.  配置 Postgres 数据库
2.  配置 Apache 配置单元
3.  解决一些错误
4.  启动阿帕奇蜂房

# 配置 Postgres 数据库

运行 Hadoop 框架后，我们可以开始设置 Apache Hive。我将假设您已经使用我的帖子[运行多节点 Hadoop 集群](/codex/running-a-multi-node-hadoop-cluster-257068e5f276)在同一页面上设置了 Hadoop 框架。选择一个 Hadoop 节点作为配置单元服务器，并在其上安装 Postgres。Apache Hive 使用一个执行引擎(默认为 Map-Reduce、Tez 或 Spark)来分布式运行查询，尽管它只能在一个服务器上使用。现在，让我们配置 Postgres 数据库。

```
[root@hiveserver ~]# sudo -u postgres psqlpostgres=# create user hive_user with encrypted password 'hive_password';
postgres=# create database hive_database;
postgres=# grant all privileges on database hive_database to hive_user;
```

# 配置 Apache 配置单元

D 下载并提取蜂箱。

```
[hduser@hiveserver ~]# wget {Apache Hive LINK (I downloaded apache-hive-3.1.2-bin.tar.gz)}
[hduser@hiveserver ~]# cd /opt
[hduser@hiveserver /opt]# sudo tar xzf /home/hduser/apache-hive-3.1.2-bin.tar.gz
[hduser@hiveserver /opt]# sudo ln -s apache-hive-3.1.2-bin/ hive
[hduser@hiveserver /opt]# sudo chown -R hduser:hadoop hive
[hduser@hiveserver /opt]# sudo chown -R hduser:hadoop apache-hive-3.1.2-bin
```

所有配置单元配置文件都在目录`/pot/hive/conf`下。现在，编辑配置单元配置文件。

```
[hduser@hiveserver ~]# cd /opt/hive/conf/
[hduser@hiveserver /opt/hive/conf]# cp hive-env.sh.template hive-env.sh
[hduser@hiveserver /opt/hive/conf]# vi hive-env.sh# Add these commands
HADOOP_HOME=/opt/hadoop
export HIVE_HOME=/opt/hive
export HIVE_CONF_DIR=${HIVE_HOME}/conf[hduser@hiveserver /opt/hive/conf]# cp hive-default.xml.template hive-site.xml
[hduser@hiveserver /opt/hive/conf]# vi hive-site.xml# Add these elements
  <property>
    <name>system:java.io.tmpdir</name>
    <value>/tmp/hive/java</value>
  </property>
  <property>
    <name>system:user.name</name>
    <value>${user.name}</value>
  </property># Find javax.jdo.option.ConnectionUserName property and change its value to username of Postgres database: hive_user# Find javax.jdo.option.ConnectionPassword property and change its value to username of Postgres database: hive_password# Find javax.jdo.option.ConnectionURL property and change its value to username of Postgres database: jdbc:postgresql://hiveserver:5432/hive_database# Find hive.server2.enable.doAs property and change its value to false
```

Hive 使用 HDFS 的一些目录作为临时目录或存储数据。

```
[hduser@hiveserver ~]# # Create Hive directories
[hduser@hiveserver ~]# ## Create warehouse and temp directories
[hduser@hiveserver ~]# hdfs dfs -mkdir /tmp
[hduser@hiveserver ~]# hdfs dfs -chmod g+w /tmp
[hduser@hiveserver ~]# hdfs dfs -mkdir -p /user/hive/warehouse
[hduser@hiveserver ~]# hdfs dfs -chmod g+w /user/hive/warehouse
[hduser@hiveserver ~]# ## Check permissions to be drwxrwxr-x
[hduser@hiveserver ~]# hdfs dfs -ls /
[hduser@hiveserver ~]# hdfs dfs -ls /user/hive
```

是时候设置环境变量了。

```
[hduser@hiveserver ~]# vi .bashrc# Add these commands
# Hive
export HIVE_HOME=/opt/hive
export HIVE_CONF_DIR=$HIVE_HOME/conf
export PATH=$HIVE_HOME/bin:$PATH
```

好吧！休息一下！

![](img/e171c68d474c0a89f3309a49786e613d.png)

照片由[穆罕默德·达德哈](https://unsplash.com/@mohammaddadkhah?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 解决一些错误

虽然我们已经正确配置了配置单元，但是您可能会遇到一些错误。第一个是`com.ctc.wstx.WstxParsingException`类型的奇特`RuntimeException`。

```
Exception in thread "main" java.lang.RuntimeException: com.ctc.wstx.exc.WstxParsingException: Illegal character entity: expansion character
```

我不知道为什么会这样，因为我们复制了模板配置文件并编辑了它们。但是解决方案很简单，我们需要删除`hive-site.xml`中的一个特殊字符。

```
[hduser@hiveserver /opt/hive/conf]# vi hive-site.xml# Find hive.txn.xlock.iow property and delete the special character between for and transactional words in its description.
```

另一个错误更奇怪。

```
Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions.checkArgument(ZLjava/lang/String;Ljava/lang/Object;)
```

发生此错误是因为库版本之间不一致。解决此错误需要小版本控制。检查 Apache Hive 和 Apache Hadoop 中`guava`库的版本，将最新的复制到对方。

```
[hduser@hiveserver ~]# ls /opt/hive/lib/ | grep guava
guava-19.0.jar[hduser@hiveserver ~]# ls /opt/hadoop/share/hadoop/hdfs/lib/ | grep guava
guava-27.0-jre.jar
```

在我的例子中，我应该将`guava` jar 文件从 Hadoop 库复制到 Hive 库。

```
[hduser@hiveserver ~]# mv /opt/hive/lib/guava-19.0.jar /opt/hive/lib/guava-19.0.jar.bak
[hduser@hiveserver ~]# cp /opt/hadoop/share/hadoop/hdfs/lib/guava-27.0-jre.jar /opt/hive/lib/guava-27.0-jre.jar
```

希望不会再有错误了。让我们开始蜂巢。

# 启动阿帕奇蜂房

使用这些命令启动配置单元服务器和元存储服务器。

```
[hduser@hiveserver ~]# hiveserver2
[hduser@hiveserver ~]# hive --service metastore
```

您还可以创建服务来控制配置单元服务器和元存储服务器。

```
[hduser@hiveserver ~]# cd /etc/systemd/system
[hduser@hiveserver /etc/systemd/system]# sudo vi hive.service# Add these lines
[Unit]
Description=Hive Server 2
After=syslog.target network.target remote-fs.target nss-lookup.target network-online.target
Requires=network-online.target hdfs.service yarn.service[Service]
User=hduser
Group=hadoop
Type=simple
ExecStart=/opt/hive/bin/hiveserver2
WorkingDirectory=/home/hduser
TimeoutStartSec=2min
Restart=on-failure[Install]
WantedBy=multi-user.target[hduser@hiveserver /etc/systemd/system]# sudo vi hive-metastore.service# Add these lines
[Unit]
Description=Hive Meta Store Server
After=syslog.target network.target remote-fs.target nss-lookup.target network-online.target
Requires=network-online.target hdfs.service yarn.service hive.service[Service]
User=hduser
Group=hadoop
Type=simple
ExecStart=/opt/hive/bin/hive --service metastore
WorkingDirectory=/home/hduser
TimeoutStartSec=2min
Restart=on-failure[Install]
WantedBy=multi-user.target
```

我希望这个教程是有用的。谢谢你。