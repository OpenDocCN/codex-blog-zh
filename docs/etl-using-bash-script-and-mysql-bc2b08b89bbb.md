# 使用 Bash 脚本和 MySQL 的数据工程

> 原文：<https://medium.com/codex/etl-using-bash-script-and-mysql-bc2b08b89bbb?source=collection_archive---------4----------------------->

## 通过学习 Bash 脚本，提高您的数据加载和 ETL(提取、转换和加载)技能。

![](img/44a11b0f334c6a4f666bad1cd8f09828.png)

凯文·Ku 在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

# **问题陈述**

我们先来了解一下问题陈述。假设来自不同源系统的数据作为 CSV 文件被提供到一个公共目录中。您的工作是创建表，从目录中读取数据，如果是满负载，则截断表，如果是增量负载，则只将数据加载到 MySQL 数据库中。进行一些基本的转换，并将数据加载到转换表中。将转换表上传为 CSV 格式的业务文件，并将源文件存档。并且每天安排工作。

# 使用 Bash 脚本的 ETL

我们将使用**产品(P_ID，产品名称，描述)**主数据、**客户(C_ID，客户名称，年龄)**主数据和**销售(S_ID，C_ID，P_ID，数量，金额)**交易数据。假设 PRODUCT.csv 文件共享在/home/user/PRODUCT 目录中，CUSTOMER.csv 文件共享在/home/user/CUSTOMER 目录中，SALES.csv 文件共享在/home/user/SALES 目录中。

让我们将 PRODUCT.csv 文件加载到数据库中。

加载 CUSTOMER.csv 文件的步骤相同

对于 SLAES.csv 文件，我们将遵循类似的步骤，只是我们不会截断该表，因为它是一个事务表。

现在所有的数据都被加载到 MySQL 数据库中。现在，我们将开发一个存储过程来填充转换表，并从脚本中运行 SP。SP 的目的是用客户名称、产品名称、数量和金额填充转换表。

现在，我们将从脚本中调用存储过程，从转换表中生成一个文件，并上传业务文件，然后将其从目录中删除。

现在我们需要编写一个 cronjob 来定期调度任务。该任务现在将在每天下午 23:59 运行。

我们已经使用 bash 脚本完成了 ETL 之旅。

感谢您的阅读！

敬请关注更多内容。