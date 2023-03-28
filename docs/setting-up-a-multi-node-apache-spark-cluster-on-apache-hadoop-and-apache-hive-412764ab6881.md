# 在 Apache Hadoop 和 Apache Hive 上设置多节点 Apache Spark 集群

> 原文：<https://medium.com/codex/setting-up-a-multi-node-apache-spark-cluster-on-apache-hadoop-and-apache-hive-412764ab6881?source=collection_archive---------4----------------------->

Apache Spark 是一个多语言引擎，用于在单节点机器或集群上执行数据工程、数据科学和机器学习。在这篇文章中，我将在 Apache Hadoop 3.3.0 上安装 Apache Spark 3.0.1。由于应该有一个正在运行的 Apache Hadoop 集群来设置 Apache Spark 集群，我建议使用我以前的博客文章[运行多节点 Hadoop 集群](/codex/running-a-multi-node-hadoop-cluster-257068e5f276)来设置您的 Apache Hadoop 集群。

[](/codex/running-a-multi-node-hadoop-cluster-257068e5f276) [## 运行多节点 Hadoop 集群

### Apache Hadoop 软件库是一个框架，允许跨…分布式处理大型数据集

medium.com](/codex/running-a-multi-node-hadoop-cluster-257068e5f276) ![](img/77bfcf3609332118c17be3500c66cc41.png)

[阿帕奇火花](https://spark.apache.org/)

我可能会使用一些 Scala 代码和 CentOS (RHEL)命令。

# 设置 Apache Spark 环境

运行 Hadoop 框架后，我们可以开始设置 Apache Spark。我将假设您已经使用我的帖子[运行多节点 Hadoop 集群](/codex/running-a-multi-node-hadoop-cluster-257068e5f276)设置了 Hadoop 框架，并在同一页面上。现在，下载并复制文件给所有工人。

```
[hduser@master ~]# wget {Apache Spark LINK (I downloaded spark-3.0.1-bin-hadoop3.2.tgz)}
[hduser@master ~]# scp spark-3.0.1-bin-hadoop3.2.tgz hduser@worker1:/home/hduser/spark-3.0.1-bin-hadoop3.2.tgz
[hduser@master ~]# scp spark-3.0.1-bin-hadoop3.2.tgz hduser@worker2:/home/hduser/spark-3.0.1-bin-hadoop3.2.tgz
```

**在所有服务器**上，提取 Apache Spark，并创建一个软链接，以便将来能够简单地更改其版本。我将解压`/opt`目录中的所有文件。

```
[hduser@{server} ~]# cd /opt
[hduser@{server} /opt]# sudo tar xzf /home/hduser/spark-3.0.1-bin-hadoop3.2.tgz
[hduser@{server} /opt]# sudo ln -s spark-3.0.1-bin-hadoop3.2/ spark
[hduser@{server} /opt]# sudo chown -R hduser:hadoop spark
[hduser@{server} /opt]# sudo chown -R hduser:hadoop spark-3.0.1-bin-hadoop3.2
```

# 配置 Apache Spark

B 在开始配置过程之前，我应该提到在 Apache Hadoop 集群上配置 Apache Spark 集群是一个两步过程。首先，Spark 应该知道 Hadoop 是如何工作的，然后它应该知道它应该如何工作！让我给你看看代码。将 Hadoop 的配置文件复制到所有服务器上 Spark 的配置目录中。所有的 Spark 配置文件都在目录`/pot/spark/conf`下。

```
[hduser@{server} ~]# cd /pot/spark/conf
[hduser@{server} /opt/spark/conf]# cp /opt/hadoop/etc/hadoop/core-site.xml .
[hduser@{server} /opt/spark/conf]# cp /opt/hadoop/etc/hadoop/hdfs-site.xml .
[hduser@{server} /opt/spark/conf]# cp /opt/hadoop/etc/hadoop/yarn-site.xml .
```

在主服务器上，编辑 Spark 的配置文件，并将它们复制到 workers。

```
[hduser@master ~]# cd /pot/spark/conf
[hduser@master /opt/spark/conf]# cp slaves.template slaves
[hduser@master /opt/spark/conf]# cp spark-env.sh.template spark-env.sh
[hduser@master /opt/spark/conf]# cp log4j.properties.template log4j.properties
[hduser@master /opt/spark/conf]# vi slaves# Add the names of all workers
master
worker1
worker2[hduser@master /opt/spark/conf]# vi spark-env.sh# Add these lines
export JAVA_HOME=/opt/jdkexport SPARK_HOME=/opt/spark
export SPARK_CONF_DIR=$SPARK_HOME/conf
export HADOOP_HOME=/opt/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoopexport SPARK_WORKER_DIR=/tmp/spark/work[hduser@master /opt/spark/conf]# vi log4j.properties# Add this line
log4j.logger.org=OFF[hduser@master /opt/spark/conf]# scp log4j.properties spark-env.sh hduser@{slaves}:/opt/spark/conf/
```

最后，编辑`.bashrc`文件，并添加这几行。

```
# Spark
export SPARK_HOME=/opt/spark
export SPARK_CONF_DIR=$SPARK_HOME/conf
export PATH=$SPARK_HOME/bin:$PATH
```

# 提供 Apache 火花罐文件

Apache Spark 的配置已经完成，但是在提交任何 Spark 应用程序之前，我们应该提供 Spark JAR 文件。简单地说，在任何服务器上运行这些命令！

```
[hduser@{server} ~]# hdfs dfs -mkdir -p /user/spark/share/lib
[hduser@{server} ~]# hadoop fs -put /opt/spark/jars/* /user/spark/share/lib/
```

在这里，您可以提交 Spark 应用程序，并在任何服务器上使用类似这样的命令来使用 Apache HDFS 和 Apache Yarn。

```
[hduser@{server} ~]# /opt/spark/bin/spark-submit \
--master yarn --deploy-mode cluster \
--class com.github.saeiddadkhah.hadoop.spark.Application \
--driver-memory 2G --driver-cores 2 \
--executor-memory 1G --executor-cores 1\
--name MyApp \
--num-executors 10 \
MyApp.jar
```

您应该在应用程序中为 Apache Spark 提供一些其他配置。下面的代码是 Scala 代码，但是 Apache Spark 为其他语言提供了类似的 API。

火花配置

# 添加 Apache 配置单元支持

决定是否在 Spark 应用程序中使用 Apache Hive 是您的责任，这完全取决于用例以及您的偏好。在这里，我将只配置 Apache Spark 来使用 Apache Hive 3.1.2。自然，我们需要一个运行中的 Apache Hive，好消息是您可以使用我以前的帖子[设置 Apache Hive 数据仓库](/codex/setting-up-an-apache-hive-data-warehouse-6074775cf66)来设置它。

[](/codex/setting-up-an-apache-hive-data-warehouse-6074775cf66) [## 设置 Apache Hive 数据仓库

### Apache Hive 数据仓库软件有助于在 Hadoop 中使用 SQL 读取、写入和管理大型数据集…

medium.com](/codex/setting-up-an-apache-hive-data-warehouse-6074775cf66) 

现在，将 Hive 的配置文件复制到所有服务器上 Spark 的配置目录中。

```
[hduser@{server} ~]# cd /pot/spark/conf
[hduser@{server} /opt/spark/conf]# cp /opt/hive/conf/hive-site.xml .
```

您还应该在应用程序中添加其他配置来启用配置单元支持。

带配置单元的火花配置

请继续关注下一篇文章来设置更多的工具。我希望你喜欢这个教程。谢谢你。