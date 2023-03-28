# 运行多节点 Hadoop 集群

> 原文：<https://medium.com/codex/running-a-multi-node-hadoop-cluster-257068e5f276?source=collection_archive---------5----------------------->

Apache Hadoop 软件库是一个框架，允许跨计算机集群分布式处理大型数据集。建立一个 Hadoop 集群并不难，但是没有一个统一的文档，所以我决定写一篇关于它的文章。让我们开始吧…

![](img/3de99052c89f6f17436551a4b652db97.png)

阿帕奇 Hadoop[[https://hadoop.apache.org/](https://hadoop.apache.org/)

假设我们有三台 Linux 服务器，一台是主服务器，其他的是 worker1 和 worker2。我可能会使用一些 CentOS (RHEL)命令。我们将通过以下步骤来运行集群。

1.  准备服务器
2.  安装 JDK
3.  配置 HDFS 和纱线
4.  启动 HDFS 和纱线守护进程

# 准备服务器

由于 HDFS 和 YARN 使用主机名进行通信，我们应该为所有服务器设置主机名，并将它们添加到`/etc/hosts`文件中。让我们假设服务器的 IP 地址是`192.168.0.1`、`192.168.0.2`和`192.168.0.3`。`SSH`每台服务器，并将这些行复制到`/etc/hosts`文件中。

```
[root@192.168.0.x ~]# vi /etc/hosts# Add these lines:
192.168.0.1   master
192.168.0.2   worker1
192.168.0.3   worker2
```

我将使用这个符号`[{user}@{server} {working directory}]`来指定应该运行命令的*用户*、*服务器*和*工作目录*，所以在运行脚本和命令之前检查它们。现在，是时候设置`hostname`了。`SSH`每台服务器，使用以下命令设置相应的主机名，并重新启动它们:

```
[root@192.168.0.x ~]# hostnamectl set-hostname {name} # name: master, worker1, worker2
[root@192.168.0.x ~]# hostnamectl # Should print {name}
[root@192.168.0.x ~]# reboot
```

使用以下命令，在`hadoop`组中创建一个名为`hduser`的用户，使其成为`sudoer`，并为其设置密码:

```
[root@{hostname} ~]# groupadd hadoop
[root@{hostname} ~]# useradd -m hduser
[root@{hostname} ~]# usermod -aG hadoop hduser
[root@{hostname} ~]# usermod -aG wheel hduser
[root@{hostname} ~]# passwd hduser
```

**上的`hduser`master**应该能够通过**无密码** ssh 连接到**本身和其他服务器**。我们应该在**主**服务器上为`hduser`创建一个 ssh 密钥，并将它复制到**所有服务器，包括它自己**:

```
[hduser@master ~]# ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
[hduser@master ~]# ssh-copy-id -i .ssh/id_rsa.pub hduser@{master and workers}
```

您可以验证在运行最新的命令后，我们被允许使用`hduser`ssh 所有服务器，而无需提供密码。完成这些简单的步骤后，服务器就准备好了，我们可以进入下一步了。

# 安装 Java

在使用 Apache Hadoop 之前，我们应该安装 Java，所以下载 JDK 或 JRE(我更喜欢 JDK)，并复制给 workers。

```
[hduser@master ~]# wget {JDK LINK (I downloaded jdk-8u291-linux-x64.tar.gz)}
[hduser@master ~]# scp jdk-8u291-linux-x64.tar.gz hduser@worker1:/home/hduser/jdk-8u291-linux-x64.tar.gz
[hduser@master ~]# scp jdk-8u291-linux-x64.tar.gz hduser@worker2:/home/hduser/jdk-8u291-linux-x64.tar.gz
```

**在所有服务器**上，提取 JDK，并创建一个软链接，以便将来能够简单地更改其版本。我将解压`/opt`目录中的所有文件。

```
[hduser@{server} ~]# cd /opt
[hduser@{server} /opt]# sudo tar xzf /home/hduser/jdk-8u291-linux-x64.tar.gz
[hduser@{server} /opt]# sudo ln -s jdk1.8.0_291/ jdk
[hduser@{server} /opt]# sudo chown -R hduser:hadoop jdk
[hduser@{server} /opt]# sudo chown -R hduser:hadoop jdk1.8.0_291

[hduser@{server} /opt]# sudo update-alternatives --install /usr/bin/java java /opt/jdk/bin/java 100
[hduser@{server} /opt]# sudo update-alternatives --install /usr/bin/javac javac /opt/jdk/bin/javac 100
[hduser@{server} /opt]# # Check java is installed successfully
[hduser@{server} /opt]# update-alternatives --display java
[hduser@{server} /opt]# update-alternatives --display javac
[hduser@{server} /opt]# java -version
```

# 配置 HDFS 和纱线

目前，我们已经设置好了服务器和 java！让我们下载 Apache Hadoop，将其复制到所有服务器，创建链接，等等。

```
[hduser@master ~]# wget {Apache Hadoop LINK (I downloaded hadoop-3.3.0-aarch64.tar.gz)}
[hduser@master ~]# scp hadoop-3.3.0-aarch64.tar.gz hduser@worker1:/home/hduser/hadoop-3.3.0-aarch64.tar.gz
[hduser@master ~]# scp hadoop-3.3.0-aarch64.tar.gz hduser@worker2:/home/hduser/hadoop-3.3.0-aarch64.tar.gz[hduser@{server} ~]# cd /opt
[hduser@{server} /opt]# sudo tar xzf /home/hduser/hadoop-3.3.0-aarch64.tar.gz
[hduser@{server} /opt]# sudo ln -s hadoop-3.3.0/ hadoop
[hduser@{server} /opt]# sudo chown -R hduser:hadoop hadoop
[hduser@{server} /opt]# sudo chown -R hduser:hadoop hadoop-3.3.0
```

**在所有服务器**上，创建一个目录，并将所有数据存储在那里。我将在`/data`目录下创建这个目录，所以如果您选择了另一个目录，不要忘记更改脚本。我们将创建四个目录:

*   `data`:存储与 *DataNode* 相关的数据
*   `name`:存储与 *NameNode* 相关的数据
*   `pid`:创建`pid`文件
*   `tmp`:允许 Apache Hadoop 创建临时文件

```
/opt/hadoop/etc/hadoop
[hduser@{server} /data]# sudo mkdir hadoop
[hduser@{server} /data]# sudo chown -R hduser:hadoop hadoop
[hduser@{server} /data/hadoop]# cd hadoop
[hduser@{server} /data/hadoop]# mkdir data
[hduser@{server} /data/hadoop]# mkdir name
[hduser@{server} /data/hadoop]# mkdir pid
[hduser@{server} /data/hadoop]# mkdir tmp
```

所有的 Hadoop 配置文件都在`/opt/hadoop/etc/hadoop`目录下，而且大部分配置在所有服务器上应该是相同的，所以我们将在**主**上逐个修改，然后复制到**工作器**上，并在工作器上做一些修改。

```
[hduser@master ~]# cd /opt/hadoop/etc/hadoop
[hduser@master /opt/hadoop/etc/hadoop]# vi core-site.xml# Add these elements:
## Do not forget to change the tmp directory
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://{master}:9000</value>
  </property>
  <property>
    <name>hadoop.tmp.dir</name>
    <value>/data/hadoop/tmp</value>
  </property>[hduser@master /opt/hadoop/etc/hadoop]# vi hadoop-env.sh# Add these commands:
## Do not forget to change the PID directory
export JAVA_HOME=/opt/jdk
export HADOOP_PID_DIR=/data/hadoop/pid[hduser@master /opt/hadoop/etc/hadoop]# vi hdfs-site.xml# Add these elements:
## dfs.replication property specifies replication factor, you may want to change it. I prefer replication factor of two for small clusters.
## Do not forget to change the data directory
## Do not forget to change the name directory
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
  <property>
    <name>dfs.data.dir</name>
    <value>/data/hadoop/data</value>
  </property>
  <property>
    <name>dfs.name.dir</name>
    <value>/data/hadoop/name</value>
  </property>
  <property>
    <name>dfs.permission</name>
    <value>false</value>
  </property>[hduser@master /opt/hadoop/etc/hadoop]# vi mapred-site.xml# Add these elements:
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>mapreduce.application.classpath</name>
    <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
  </property>
  <property>
    <name>mapred.job.tracker</name>
    <value>master:54311</value>
    <description>The host and port that the MapReduce job tracker runs
    at.  If "local", then jobs are run in-process as a single map
    and reduce task.
    </description>
  </property>[hduser@master /opt/hadoop/etc/hadoop]# vi workers# Add the names of all workers
master
worker1
worker2[hduser@master /opt/hadoop/etc/hadoop]# vi yarn-site.xml# Add these lines
## You may want to change the max-disk-utilization-per-disk-percentage.
## Notice that the value of "yarn.nodemanager.hostname" property is {worker}. We leave it for now, and will change it later
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.env-whitelist</name>
    <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
  </property>
  <property>
    <name>yarn.nodemanager.disk-health-checker.max-disk-utilization-per-disk-percentage</name>
    <value>95</value>
  </property>
  <property>
    <name>yarn.resourcemanager.hostname</name>
    <value>master</value>
  </property>
  <property>
    <name>yarn.nodemanager.hostname</name>
    <value>{worker}</value>
  </property>
```

好吧！搞定了。现在，我们将所有的配置文件复制到所有的工人，然后改变他们中的一些。

```
[hduser@master /opt/hadoop/etc/hadoop]# scp core-site.xml hadoop-env.sh hdfs-site.xml mapred-site.xml yarn-site.xml hduser@{workers}:/opt/hadoop/etc/hadoop/[hduser@{server} /opt/hadoop/etc/hadoop]# vi yarn-site.xml# Change the value of "yarn.nodemanager.hostname" to the name of server.
## On master: <value>master</value>
## On worker1: <value>worker1</value>
```

是时候在所有服务器上设置环境变量了。我建议在 master 上这样做，然后复制给其他人。

```
[hduser@{server} ~]# vi .bashrc# Add these commands
# Java
export JAVA_HOME=/opt/jdk# Haddop & YARN
export PDSH_RCMD_TYPE=ssh
export HADOOP_HOME=/opt/hadoop
export HADOOP_CONF_DIR=/opt/hadoop/etc/hadoop
export HADOOP_MAPRED_HOME=/opt/hadoop
export HADOOP_COMMON_HOME=/opt/hadoop
export HADOOP_HDFS_HOME=/opt/hadoop
export HADOOP_YARN_HOME=/opt/hadoop
export PATH=$HADOOP_HOME/bin:$PATH
export HDFS_NAMENODE_USER="hduser"
export HDFS_DATANODE_USER="hduser"
export HDFS_SECONDARYNAMENODE_USER="hduser"
export YARN_RESOURCEMANAGER_USER="hduser"
export YARN_NODEMANAGER_USER="hduser"
export LD_LIBRARY_PATH=$HADOOP_HOME/lib/native
```

# 启动 HDFS 和纱线守护进程

下载，共享文件，配置所有服务器！现在，是启动集群的时候了。我们应该格式化命名节点，然后开始 HDFS 和纱线。如果你想启动 HDFS 和纱，启动 HDFS，然后纱，当你想停止他们，停止纱先，然后 HDFS。

```
[hduser@master ~]# hadoop namenode -format
[hduser@master ~]# /opt/hadoop/sbin/start-dfs.sh
[hduser@master ~]# /opt/hadoop/sbin/start-yarn.sh
[hduser@master ~]# /opt/hadoop/sbin/stop-yarn.sh
[hduser@master ~]# /opt/hadoop/sbin/stop-dfs.sh
```

您还可以创建服务来控制 HDFS 和纱线。

```
[hduser@master ~]# cd /etc/systemd/system
[hduser@master /etc/systemd/system]# vi hdfs.service# Add these lines
## Do not forget to change the PID directory
[Unit]
Description=Hadoop DFS namenode and datanode
After=syslog.target network.target remote-fs.target nss-lookup.target network-online.target
Requires=network-online.target[Service]
User=hduser
Group=hadoop
Type=simple
ExecStart=/opt/hadoop/sbin/start-dfs.sh
ExecStop=/opt/hadoop/sbin/stop-dfs.sh
WorkingDirectory=/home/hduser
TimeoutStartSec=2min
Restart=on-failure
PIDFile=/data/hadoop/pid/hadoop-hduser-namenode.pid[Install]
WantedBy=multi-user.target[hduser@master /etc/systemd/system]# vi yarn.service# Add these lines
## Do not forget to change the PID directory
[Unit]
Description=YARN resourcemanager and nodemanagers
After=syslog.target network.target remote-fs.target nss-lookup.target network-online.target
Requires=network-online.target[Service]
User=hduser
Group=hadoop
Type=simple
ExecStart=/opt/hadoop/sbin/start-yarn.sh
ExecStop=/opt/hadoop/sbin/stop-yarn.sh
WorkingDirectory=/home/hduser
TimeoutStartSec=2min
Restart=on-failure
PIDFile=/data/hadoop/pid/hadoop-hduser-resourcemanager.pid[Install]
WantedBy=multi-user.target
```

我希望这个教程是有用的。谢谢你。