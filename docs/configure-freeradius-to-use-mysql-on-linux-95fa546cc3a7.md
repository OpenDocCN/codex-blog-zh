# 配置 FreeRADIUS 在 Linux 上使用 MySql。

> 原文：<https://medium.com/codex/configure-freeradius-to-use-mysql-on-linux-95fa546cc3a7?source=collection_archive---------2----------------------->

![](img/9079e68a51a306395e323da726b58e38.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com/s/photos/authentication?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

由于工作原因，我不得不在 Linux 服务器上配置 FreeRADIUS(Ubuntu 和 Red Hat ),这花费了我很多时间来获得所有的信息来完成这个任务。因此，我想用我收集的所有信息写一个介绍性的指南，以便为应该完成同样任务的人减轻工作负担。

## 什么是 FreeRADIUS？

**FreeRADIUS** 是 RADIUS 的开源实现，RADIUS 是用于 AAA 的 IETF 协议([授权](https://networkradius.com/blog/security/authorization-authorized-personnel-only/index.html)、[认证](https://networkradius.com/blog/security/authentication-is-it-really-you-part-1-of-our-3-part-series/index.html)和[记账](https://networkradius.com/blog/security/accounting-keeping-track-of-it-all-part-3-of-our-3-part-series/index.html))。更多信息可以在 FreeRADIUS 网站上找到，在那里你可以找到[整个文档](https://freeradius.org/documentation/#getting_started)以及[快速启动指南](https://wiki.freeradius.org/guide/Getting%20Started)来快速启动你的 RADIUS 服务器。

## 安装。

基于 Linux Debian 和基于 Centos 的 FreeRADIUS 的安装和配置略有不同。这主要是因为您可以在不同的文件夹中找到配置文件。

以下指南是为 RHEL8 和 Ubuntu 20.04.3 LTS 版编写的。对于每一步，您都可以找到适用于 Ubuntu 和 Red Hat 的指令(如果未指定，则意味着这两种环境下的指令是相同的)。

```
#Installation on Ubuntu
apt-get install freeradius freeradius-mysql freeradius-utils#Installation on RedHat
yum install freeradius freeradius-utils freeradius-mysql freeradius-perl
```

## 安装 MySQL 服务器。

```
#RedHat
yum install mysql-server#Ubuntu
apt-install mysql-server
```

## 启动 MySQL 服务器

```
#RedHat
service mysqld start#Ubuntu
systemctl start mysql.service
```

## MySQL 服务器配置。

默认情况下， **FreeRADIUS** 从配置文件中读取记账和认证信息。如果您想设置 FreeRADIUS 从一个 **MySQL** 数据库中读取信息，请使用以下步骤。

首先，运行工具来配置安全安装

```
mysql_secure_installation
```

根据用户/密码和安全性方面的需要，回答以下问题。我的情况，回答如下。

```
Enter current password **for** root (enter **for** none): ENTERSet root password? [Y/n] yNew password: Enter passwordRe-enter **new** password: Repeat passwordRemove anonymous users? [Y/n]: YDisallow root login remotely? [Y/n]: YRemove test database and access to it? [Y/n]: YReload privilege tables now? [Y/n]: Y
```

现在，我们必须创建 radius 数据库。它是指 FreeRADIUS 从其中读取会计和授权信息的数据库。首先，我们登录 MySQL 并创建一个数据库，我们称之为*。密码下面是您在上一步中配置的用于访问 MySQL 的 root 密码。*

```
*mysql -uroot -ppassworddatabasemysql> CREATE DATABASE radius;mysql> exit;*
```

*一旦我们有了数据库，我们必须创建所需的表。这可能很乏味，但幸运的是，FreeRADIUS MySQL 包包含创建这些表的查询和模式。要导入架构并填充 radius DB，请运行以下命令。*

```
*#Reh Hat
mysql -uroot -ppassword radius < /etc/raddb/mods-config/sql/main/mysql/schema.sql#Ubuntu
mysql -uroot -ppassword radius < /etc/freeradius/3.0/mods-config/sql/main/mysql/schema.sql*
```

*现在我们必须启用 ***sql*** 模块，这可以通过在 ***sql*** 模块之间创建一个软链接来完成，该模块位于文件夹 mods-available 和文件夹 mods-enabled 上。*

```
*#Red Hat
ln -s /etc/raddb/mods-available/sql /etc/raddb/mods-enabled/#Ubuntu
ln -s /etc/freeradius/3.0/mods-available/sql /etc/freeradius/3.0/mods-enabled/*
```

*为了配置 FreeRADIUS 以使用我们刚刚创建的 RADIUS 数据库，我们必须编辑两个文件:*

*   */etc/raddb/mods 下的文件***SQL***——可用(Red Hat 上)和/etc/freeradius/3.0/mods 下的文件——可用(Ubuntu 上)*
*   *文件 ***默认*** 在/etc/raddb/sites-enabled 下(在 Red Hat 上)和/etc/freeradius/3.0/sites-enabled 下(在 Ubuntu 上)*

*让我们从第一个文件开始。这里，我们必须配置 FreeRADIUS 连接到 RADIUS 数据库所需的信息。关于这个文件的一些提示。与 FreeRADIUS 的许多配置文件一样，这个文件比下面的文件长得多，因为所有的配置文件都有许多被注释掉的行，这些行主要是关于配置的提示。这里有很多解释，我建议你看看这些评论，了解如何配置你的服务器。它们真的很有用，很有解释力。*

*下面只是一个摘要，其中包含了您需要更改的主要信息。关于这个文件的一些提示。*

*   *密码字段是您在上面的一些步骤中配置的 MySQL 密码。*
*   *作为驱动和方言，你得配置 MySQL。这通知 FreeRADIUS 您正在使用 MySQL 作为 SQL 数据库。*
*   ****radius _ db = " radius "***是指 FreeRADIUS 要使用一个叫做***【radius】***的 MySQL 数据库，就是我们之前配置的那个。因此，如果您使用了另一个名称，请相应地更改下面的文件。*
*   ****client _ table = " nas "***通知 FreeRADIUS 关于 NAS 客户端的信息在一个名为 ***"nas "的表中。****
*   *如果下面的信息有注释，请取消注释。*

```
*sql {driver = "rlm_sql_mysql"dialect = "mysql"# Connection info:server = "localhost"port = 3306login = "radius"password = "radiuspassword"# Database table configuration **for** everything except Oracleradius_db = "radius"}read_clients = yesclient_table = “nas”*
```

*第二个文件通知 FreeRADIUS 使用数据库或配置文件来查找有关授权和记帐的信息。默认情况下，sql 标记被注释掉(因为默认情况下，FreeRADIUS 使用配置文件进行所有关于记帐、授权等操作)。因此，取消 authorize、accounting、post-auth、session 部分下的 sql 标记的注释。您应该得到类似下面的文件。*

```
*authorize {.....sql....}accounting {......sql....}post-auth {......sql....}session{......sql.....}*
```

*在我的情况下，当我试图启动 FreeRADIUS 时，我有一个 SSL 证书找不到的错误。要避免这个错误，只需取消/etc/raddb/mods-enabled(在 Red Hat 上)和/etc/freeradius/3.0/MODS-enabled(在 Ubuntu 上)下的文件 **sql** 中与 TLS 加密相关的部分的注释即可。*

```
*mysql {# If any of the files below are set, TLS encryption is enabledtls {#ca_file = "/etc/ssl/certs/my_ca.crt"#ca_path = "/etc/ssl/certs/"#certificate_file = "/etc/ssl/certs/private/client.crt"#private_key_file = "/etc/ssl/certs/private/client.key"#cipher = "...."#tls_required = yes#tls_check_cert = no#tls_check_cert_cn = no}# If yes, (or auto and libmysqlclient reports warnings are# available), will retrieve and log additional warnings from# the server **if** an error has occured. Defaults to 'auto'warnings = auto}*
```

## *开始 FreeRADIUS。*

*要在调试模式下启动 Freeradius，请运行以下命令:*

```
*#Red Hat
radiusd -X#Ubuntu
freeradius -X*
```

*大多数时候，每次我运行上面的命令，我都会遇到一个错误，默认端口已经被使用。当 FreeRADIUS 守护程序已经启动时，会发生这种情况。在这种情况下，使用以下命令终止守护程序*

```
*pkill radiusd*
```

*再试一次。*

## *测试配置。*

*为了测试 radius 服务器是否收到认证请求，我们可以使用 FreeRADIUS 实用程序提供的 ***radtest*** 工具。*

*最初，您可以使用下面的命令测试本地主机。*

```
*radtest testing password 127.0.0.1 0 testing123*
```

*上面的命令可以这样理解。认证为**测试/密码**的用户向本地主机发送认证请求( **127.0.0.1** )。另外两个参数是 NAS 端口(通常为零。我认为您在这里放什么并不重要)和 **testing123** 是 NAS 客户端将用于向 Radius 服务器验证自身的密码。*

*如果 Radius 服务器回复访问接受消息，这意味着您的配置有效，否则您必须仔细检查调试日志中的消息。*

*这个默认用户 **testing/password** 通常是预配置的，但如果不是，请检查/etc/freeradius/3.0/MODS-config/files(Ubuntu)或/etc/raddb/MODS-config/files(Red Hat)中的文件 **accounting** 。在最顶端，您应该会看到这样一个条目*

```
*testing Cleartext-Password := "password"*
```

*如果注释了，请将其取消注释。*

## *填充表格。*

*现在，我们想测试 FreeRADIUS 是否允许对从 MySQL 数据库读取信息的用户进行身份验证。为此，首先我们必须在 radius 数据库中填充我们的表。*

*Radius 服务器从 NAS 客户端接收身份验证请求。NAS 客户端必须向 Radius 服务器进行身份验证，提供其 IP 地址和身份验证密码。*

*Radius 服务器检查从 NAS 提供的信息是否与包含在 **nas** 表中的信息相匹配。*

*要填充 NAS 表，请运行以下命令:*

```
*mysql -uroot -ppassworddatabase radiusmysql> INSERT INTO  nas VALUES (NULL ,  '10.10.10.1',  'nas client name',  'other', NULL ,  'passwordnas', NULL , NULL ,  'RADIUS Client nas1');*
```

*上表中 NAS 名称为**‘NAS Client’**，IP 地址为 **10.10.10.1** ，认证密码为’**password NAS**，‘**RADIUS Client NAS 1**只是一个描述。*

*然后 Radius 服务器检查用户是否被授权使用网络。为此，它检查 Radius 认证消息中包含的信息是否与 **radcheck** 表中包含的信息相匹配。*

*要填充 **radcheck** 表，运行以下命令:*

```
*mysql -uroot -ppassworddatabase radiusmysql> INSERT INTO radcheck VALUES (NULL , 'user1', 'Cleartext-Password', ':=', 'passworduser1');*
```

*在本例中，我们配置了一个名为“ **user1** ”的用户，其密码为“ *passworduser1* ”。*

*如果用户获得授权，Radius 服务器会回复一条身份验证接受消息，然后检查授权后操作。特定用户的 post 授权操作包含在表 **radreply** 中。*

*例如，在我们的例子中，我们希望 Radius 服务器为用户分配一个静态 IP 地址。为此，我们使用 Framed-IP-Address 属性。*

*要填充表 **radreply** 运行以下命令*

```
*mysql -uroot -ppassworddatabase radiusmysql> INSERT INTO radreply VALUES (NULL , 'user1', 'Framed-IP-Address', ':=', '10.10.10.2');*
```

*根据该条目，如果用户“**用户 1** 被授权访问网络，他应该会收到 IP 地址 **10.10.10.2** 。*

*因此，现在我们可以使用与之前相同的 **radtest** 命令来测试我们的配置，但是要针对我们刚刚在数据库中配置的用户进行调整。*

```
*radtest user1 passworduser1 127.0.0.1 0 passwordnas*
```

*如果配置有效，您应该会收到一条消息 Access-Accept 和另一条分配了 IP 地址的消息。*

## *启动 FreeRADIUS 守护程序。*

*现在，如果一切正常，并且您想要启动 FreeRADIUS as 服务，请运行以下命令:*

```
*#Ubuntu
systemctl start freeradius.service#Red Hat
service radiusd start*
```

***来源。***

*我在这里找到了非常有用的信息:*

*[](https://draculaservers.com/tutorials/freeradius-centos-7-mysql/) [## 使用 MySQL / MariaDB - Dracula 服务器在 CentOS 7 上安装和配置 FreeRADIUS 教程

### 什么是 FreeRADIUS？FreeRADIUS 是最受欢迎的开源 RADIUS 服务器。它支持所有常见的身份验证…

draculaservers.com](https://draculaservers.com/tutorials/freeradius-centos-7-mysql/)  [## 入门指南

### 本页介绍如何执行 FreeRADIUS 的初始配置。它假定您对 Unix 系统有基本的了解…

wiki.freeradius.org](https://wiki.freeradius.org/guide/Getting-Started)*