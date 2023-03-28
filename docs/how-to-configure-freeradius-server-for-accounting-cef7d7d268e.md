# 如何为记帐配置 FreeRadius 服务器？

> 原文：<https://medium.com/codex/how-to-configure-freeradius-server-for-accounting-cef7d7d268e?source=collection_archive---------2----------------------->

![](img/99a374864ae81c4ec9062061e65806bb.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是 FreeRadius 配置系列的第二部分。这一次，我们将了解如何配置 FreeRadius 进行会计核算，以及该过程是如何工作的。

## **先决条件**

本文假设您已经在服务器上安装了 FreeRadius 和 MySQL，并且已经正确配置了它们来处理授权过程。

[如果你想知道如何安装和配置 FreeRadius，它可以和 MySQL 一起工作以获得授权，请查看我的第一篇文章](/codex/configure-freeradius-to-use-mysql-on-linux-95fa546cc3a7)。

## 什么是半径核算？

Radius 记帐收集有关用户如何使用网络的数据，用于统计和监控目的，以及对用户进行计费。当用户被授权访问网络时，计费过程开始。当 **NAS(网络接入服务器)**接收到**接入接受**消息时，它向 Radius 服务器发回该特定用户的**计费请求**，通知服务器该用户已经开始接入网络。基于特定用户在 Radius 服务器上的配置，可以计算几个参数，并且基于这些计数器的结果，可以拒绝/撤销该用户的授权。

举个例子吧。假设我们希望用户 mike 每天只能使用网络 1800 秒。当 mike 第一次被授权访问网络时，服务器端的计时器开始计时。现在，每当 mike 使用网络时，计数器都会更新，当它达到 1800 秒的上限时，对 mike 的授权就会被撤销或拒绝。拜拜，迈克！

## 自由半径上的会计配置。

在详细检查该过程如何在 FreeRadius 上工作以及该过程涉及哪些 SQL 表之前，让我们看看如何配置它。下面的指南是基于 RHEL8 的，但是这个过程对于 Ubuntu 来说是完全一样的。唯一的区别是可以找到配置文件的文件夹的路径。要了解 Ubuntu 上的路径，请查看本系列的第一篇文章。

首先，我们必须启用 ***sqlcounter*** 模块。为此，运行以下命令

```
cd /etc/raddb/mods-enabled
sudo ln -s ../mods-available/sqlcounter sqlcounter
```

***sqlcounter*** 模块可以执行几种类型的计数器。要检查它们，用一个简单的 vi 命令打开 ***sqlcounter*** 文件。

```
vi /etc/raddb/mods-available/sqlcounter
```

为了简单起见，我们只检查每日计数器

```
*sqlcounter dailycounter {sql_module_instance = sqldialect = ${modules.sql.dialect}counter_name = Daily-Session-Timecheck_name = Max-Daily-Sessionreply_name = Session-Timeoutkey = User-Namereset = daily$INCLUDE ${modconfdir}/sql/counter/${dialect}/${.:instance}.conf}*
```

****dailycounter*** 配置(像所有其他计数器一样)可以在路径 mods-config/sql/counter/mysql/下找到*

*在这里您可以找到模块 ***sqlcounter*** 所使用的默认计数器，它们是:*

*   ***每日计数器***
*   ***过期登录***
*   *月度会计*
*   ***noresetcounter***

*上述每个配置文件都包含对 MySQL 数据库上的表 **radacct** 执行的 SQL 查询。*

*为了使 FreeRadius 能够使用 ***dailycounter*** ，我们必须修改两个文件:*

*   *文件**默认**在/etc/raddb/sites-enabled 下*
*   */etc/raddb 下的文件 **radiusd.conf***

*对于第一个文件，我们运行命令*

```
*vi /etc/raddb/sites-enabled/**default***
```

*然后在授权部分添加**每日计数器**，如下图*

```
*authorize {.....sqldailycounter....}*
```

*对于第二个文件，我们运行以下命令*

```
*vi /etc/raddb/radiusd.conf*
```

*并在实例化部分下添加 **dailycounter** ，如下所示*

```
*instantiate {....dailycounter.....}*
```

*现在，如果设置正确，您应该能够启动 FreeRadius。如往常一样，如果出现问题，我建议在调试模式下使用以下命令启动 FreeRadius*

```
*radiusd -X*
```

## *FreeRadius 上的流程是如何工作的？*

*让我们回到我们的朋友迈克身上。我们希望他每天只能使用网络 1800 秒。最适合我们的计数器是***Max-Daily-Session***计数器。*

*我们必须更新 ***radcheck*** 表，这样一来，首先迈克被授权访问网络，然后他总共可以访问网络 1800 秒。*

*为此，我们运行下面的 SQL 命令来更新 ***radcheck*** 表。*

```
*INSERT INTO radcheck VALUES (NULL , 'mike', 'Cleartext-Password', ':=', 'mike1');INSERT INTO radcheck VALUES (NULL , 'mike', 'Max-Daily-Session ', ':=', '1800');*
```

*好的。现在发生了什么？*

*首先，我们应该明白 ***radacct*** 表是基于 Radius 服务器接收的 ***记账请求*** 消息更新的。这种澄清有助于理解该过程的其余部分。*

*当迈克第一次尝试建立会话时， **NAS** 向 Radius 服务器发送一个带有迈克凭证的 ***访问请求*** 消息。Radius 服务器首先检查在 ***访问请求*** 消息中提供的凭证是否与 ***radcheck*** 表中的凭证相匹配。如果匹配，那么 Radius 服务器运行***daily counter***SQL 查询，查询关于迈克会话总时间的 ***radacct*** 表。由于是 mike 的第一次访问并且没有 ***记账请求*** 消息，因此****Rada CCT***表为空；因此，Radius 服务器确定用户得到授权，并向 NAS 发回 ***访问-接受*** 消息。**

**现在 NAS 回复一个 ***记账请求*** 消息。 ***记账请求*** 包含一系列信息，例如用户 ID、网络地址、唯一会话标识符，以及其他三个参数，这些参数有助于我们理解最大每日会话的示例**

*   **事件时间戳**
*   **帐户会话时间**
*   **账户状态类型**

**第一个是 ***记账-请求*** 消息的时间戳(因此当会话开始时)，第二个是会话的持续时间，第三个是请求的类型(如果会话被启动，则可以是**开始**，或者当会话被拆除时，可以是**停止**)。**

**在我们的示例中， ***会计请求*** 消息包含以下值:**

*   **用户名= "迈克"**
*   **事件时间戳= 2022–10–11 11:49:34**
*   **帐户-会话-时间= 0**
*   **账户状态类型=开始**

*****radacct*** 表现在根据我们刚刚收到的 ***会计-请求*** 消息进行更新。具体来说，我们感兴趣的列有:**

*   **acctstarttime**
*   **acctstoptime**
*   **acctsessiontime**

**第一个指示会计过程开始的确切时间。第二个是它停止的时间，第三个是前两者之间的秒数差(并以秒为单位指示会话持续时间)。**

**如果我们现在检查 ***radacct*** 表，我们会发现只有一个条目，并且只有***acctstarttime***有值，而其他两个是空的，因为会话正在进行。**

*   **acct start time = 2022–10–11 11:49:34**
*   **acctstoptime = NULL**
*   **acctsessiontime = 0**

**现在，mike 决定取消会议。NAS 再次发送带有以下信息的*计费请求消息(以及其他信息):***

*   ***用户名= "迈克"***
*   ***事件时间戳= 2022–10–11 12:23:46***
*   ***帐户-会话-时间= 2052***
*   ***账户状态类型=停止***

***当 Radius 服务器收到消息时，它将使用新值更新 **radacct** 表的第一条记录。***

*   ***acct start time = 2022–10–11 11:49:34***
*   ***acctstoptime = 2022–10–11 12:23:46***
*   ***acctsessiontime = 2052***

***好的，如果 mike 想要建立一个新的会话，现在会发生什么呢？首先，NAS 再次发送 ***访问请求*** 消息，Radius 服务器检查凭证是否与表中的凭证匹配。我们已经知道有匹配…所以服务器运行 ***dailycounter*** 来检查***acctsessiontime***是否低于 1800 秒。***

**但是等等，在我们的例子中***acctsessiontime***是 2052，所以它比 1800 秒大。因此，Radius 服务器将发送回**访问拒绝**消息，并且会话不能建立。**

**拜拜，迈克…明天新的一节课见！**

## ****结论****

**这只是一个简单的例子，说明了 ***sqlcounter*** 、 ***radcheck*** 表和 ***radacct*** 表如何一起工作。**

**你可以以同样的方式使用另一个缺省计数器，或者你可以创建你的查询，[就像在这个非常好的教程](https://mangospot.net/enable-sqlcounter-freeradius.html)中解释的那样。**

## **来源**

**[https://mangospot.net/enable-sqlcounter-freeradius.html](https://mangospot.net/enable-sqlcounter-freeradius.html)**

**[https://network radius . com/articles/2019/06/05/how-does-radius-accounting-work . html](https://networkradius.com/articles/2019/06/05/how-does-radius-accounting-work.html)**