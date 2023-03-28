# 用 Python 连接 MySQL！举个简单的例子

> 原文：<https://medium.com/codex/connect-to-mysql-with-python-with-simple-example-9fd4eb242ba8?source=collection_archive---------4----------------------->

按照这些简单的步骤用 Python 连接到 MySQL

![](img/4d54efbd6f22d002bd0c1e758f0f9c73.png)

在处理 python 项目时，很难将控制台中的所有数据收集到 excel 表格中，以便分析结果并从我们所做的工作中得出结论。将数据列表简化了每个人调试问题和深入代码的任务，我也遇到过这种情况。然后一个奇妙的问题出现在我的脑海里“为什么我不能将我的 IDE 与 MySQL DB 连接起来，以便收集数据并在以后进行分析？”因为当我们与 DB 连接时，它会收集我们得出结果所需的每一条数据。

## 我的软件规格

1.  **Anaconda 个人版 Python 3.9** (可以使用任何最新版本的 Python)[https://repo . Anaconda . com/archive/Anaconda 3-2021.11-Windows-x86 _ 64 . exe](https://repo.anaconda.com/archive/Anaconda3-2021.11-Windows-x86_64.exe)如果你愿意可以从这个链接下载。
2.  最新版本的【https://dev.mysql.com/downloads/file/?id=510038】MySQL 8 . 0 . 28，如果你愿意可以从这个链接下载-[MySQL](https://dev.mysql.com/downloads/file/?id=510038)
3.  VScode 最新版本

## 用 python 连接 MySQL 的步骤

按照以下步骤通过 python 连接 Mysql

## *1。安装 python-MySQL 连接器*

安装完 Anaconda(或任何其他 python 版本)后，打开 anaconda shell 并使用 PIP 命令安装 MySQL 连接器。如果系统中没有配置 PIP，您也可以使用 CONDA 命令进行安装。

```
pip install mysql-connector-pythonorconda install -c anaconda mysql-connector-python
```

## 2.从 python 连接到 MySQL

打开 vscode，并在工作区中添加一个新文件夹，用于我们将在流程中执行的所有工作。导入 MySQL 连接器，并通过传递 connect 命令连接到 MySQL。提供连接所需的所有凭据。在 host 中，它通常是 localhost，以防万一，如果它是不同的，你可以改变它。即使是用户，密码也应该替换为您的用户名，密码。

如果您想检查连接是否建立，那么您可以执行 print(db)。如果你在控制台中看到类似下面的语句连接就建立了，如果没有检查你自己提供的凭证。

![](img/aaebc3d2aa65e0c7c627dc12cfd11cea.png)

从 VScode 控制台

## 3.创建数据库并与之连接

创建用于在“Python”中执行“SQL”语句的“cursor”类的实例。第 10 行传递了一个 execute 命令，该命令在 MySQL db 中创建一个具有某个名称的数据库。现在，您可以通过调用 connection 对象来检查我们是否可以连接到我们创建的新数据库。

您将在 VScode 的控制台中看到类似这样的内容，这意味着与数据库和 python 建立了连接。

![](img/65b3ea454a5927f12ec04253f85d1134.png)

从 VScode 控制台

```
#Tip : How to check all the databases in the MySQL ? Its quite simple follow these steps cursor.execute(“SHOW DATABASES”) databases = cursor.fetchall() print(databases)# This will show all available databases in MySQL Environment
```

## 4.创建表的时间

一旦创建了数据库，我们就可以创建供我们使用的表了。在此之前，我们应该对表的格式、列名和这些列的数据类型有所了解。记住要有一个表格式的主键(很可能是序列号)。在我的例子中，表格格式如下。这里 SL_No 是数据类型为“int”的“主键”，person name 是“varchar”的数据类型，最后一个 phone_number 是“int”的数据类型。像这样我们应该有表格格式的想法。

![](img/4aae2785c7888560223d866fc63a8acb.png)

我的办公室 _ 员工表格式

现在，让我们执行它并用 python 创建表。

创建表的代码

如果您运行上面的代码，您将在 Mysql 的工作区中找到该表。要检查这一点，您必须打开 MySQL workspace 并执行一个新的查询，如下所示

```
# Do this in the MySQL workbench application
use database_name;
SELECT * FROM office_staff;
```

![](img/ceea868071da9320c72a77aaf73f617a.png)

在数据库中创建的表

## 5.通过 python 将数据插入到表中

要将数据插入到表中，您需要使用 python 中的“INSERT INTO”命令传递一个查询，确保写下表名和列名的准确名称。现在，您需要在名为 values 的变量中给出这些列的值。执行查询和值，并通过传递一个附加了对象名的 commit 命令来提交所有更改。可以检查值是否插入？通过刷新表格。

![](img/737ce46e55431fba3d1a88d9ede3d349.png)

插入表中的值

所以，这就是我们如何通过 Python 与 MySQL 数据库建立连接，并成功地将数据插入到表中。

> 就这些了，请继续关注我的个人资料，了解更多有趣的话题！
> 
> *看我以前的博客，如果技术有感觉怎么办，如何防止 AWS 免费轮胎的成本？*
> 
> [https://medium . com/@ sreevishnu . dharmavaram/get-your-free-one drive-5tb-storage-now-e35b c 1634506](/@sreevishnu.dharmavaram/get-your-free-onedrive-5tb-storage-now-e35bc1634506)
> 
> [*https://medium . com/@ sreevishnu . dharmavaram/I-was-charged-a-bill-in-AWS-ec2-instance-due-my-free-tire-but-how-you-can-prevent-it-ae1 d 3c 7431 BC*](/@sreevishnu.dharmavaram/i-was-charged-a-bill-in-aws-ec2-instance-during-my-free-tire-but-how-you-can-prevent-it-ae1d3c7431bc)
> 
> [*https://medium . com/@ sreevishnu . dharmavaram/what-if-technology-has-情怀-9c81adb54518*](/@sreevishnu.dharmavaram/what-if-technology-has-feelings-9c81adb54518)