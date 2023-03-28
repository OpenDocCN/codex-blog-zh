# PostgreSQL 简介

> 原文：<https://medium.com/codex/intro-to-postgresql-c8da31335c34?source=collection_archive---------7----------------------->

## 创建和查询 Postgres 数据库

![](img/6000d2d2e344ec8c9e294f91623893cb.png)

## 为什么是 Postgres

当开始一个新项目时，在选择数据库时有许多选择。您可以使用基于 JavaScript 的 MongoDB，它将数据存储为文档。虽然您可以在 MongoDB 中将相关数据链接到文档，但是如果您希望运行复杂的查询，使用基于关系的数据库可能是更好的选择。

Postgres 使用代表结构化查询语言的 SQL。这是一种人类可读的语言，能够获得非常具体的结果。Postgres、MySQL、SQLite 和 Oracle 都是以类似表格的格式存储信息的 SQL 数据库。

Postgres 的一个优点是可以使用 JSON 作为数据类型。当您希望拥有一个可以存储各种内容类型(如视频、投票、图像等)的列时，这很有帮助。选择 Postgres 的另一个原因是它的可伸缩性和与 NodeJS 上运行的应用程序的兼容性。

## 创建数据库和表

按照[步骤下载并安装](https://www.postgresql.org/download/)Postgres 后，您可以在终端中键入“psql”进入 CLI。下面是 Postgres CLI 中使用的常用命令列表，这些命令允许您查看现有的表和对数据库所做的更改。

```
\c database_name to connect\l shows all databases\d shows all tables in database\? shows all commands\h shows queries you can useq will allow to leave the list / quit
```

正如我前面提到的，SQL 可读性很强。创建数据库就像输入“创建数据库”一样简单。虽然你不需要将所有的关键字都大写，但是这样做是很常规的。

```
CREATE DATABASE database_name_here; 
//remember to always end with semicolonCREATE TABLE users (user_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY, username VARCHAR (25) UNIQUE NOT NULL;
```

上面的代码将创建一个数据库和一个名为 users 的表。每个用户都有一个唯一的整数 id。该 id 将作为创建时自动生成的主键。如果您使用过 MongoDB，那么主键相当于 _id。它用于创建独立数据表之间的关系。VARCHAR 是一种数据类型，指的是我们可以设置最大值的文本。如果我们不需要限制这个数据，我们可以使用文本类型来代替。通过包含 UNIQUE NOT NULL，我们明确表示如果用户名不唯一，将会抛出错误，并且不会保存。

要向新创建的表中添加用户，我们可以使用 INSERT INTO 关键字，后跟表名、列和这些列的值。这些值应该用单引号括起来，不要用双引号。

```
INSERT INTO users (username) VALUES ('codingchamp');
```

## 选择和查询子句

我们运行的每个查询都将以关键字 SELECT 开始。如果你想从一个表中选择所有的数据，你可以像这样运行…

```
SELECT * FROM users;
```

要获得更具体的数据，可以使用子句。子句是附加的关键字，比如 WHERE 和 ORDER BY，它允许我们进一步深入到表数据中，并减少它，只返回我们当前需要的内容。这里有一个例子。

```
SELECT user_id, username FROM users ORDER BY username DESC LIMIT 10;
```

这将返回 users 表中按用户名降序排序的前十个结果的主键和用户名。结果的默认顺序是升序，所以如果不包含 DESC 子句，就会得到这个结果。

从句的顺序很重要。尽管您可能不需要一次使用所有这些子句，但在查询时，以下是可接受的操作顺序。

1.  从，包括连接
2.  在哪里
3.  分组依据
4.  拥有
5.  窗口功能
6.  挑选
7.  明显的
8.  联盟
9.  以...排序
10.  极限和偏移

你可以在这里阅读更多关于这些条款及其用例的信息[。](https://www.eversql.com/sql-order-of-operations-sql-query-order-of-execution/)

## 更新和删除

更改表中的值就像定义我们想要设置的新值并查询我们想要更改的数据一样简单。

```
UPDATE users SET username = 'codequeen' WHERE user_id = 2;
```

删除也一样简单。

```
DELETE FROM users WHERE user_id = 2;
```

## 外键

请记住，SQL 数据库的美妙之处在于它们是关系型的，我们可以建立连接。在我们的用户表示例中，我们有一个主键，它总是在创建用户时生成，并且总是惟一的。我们可以将该主键映射到另一个表来引用该用户实例。这被称为外键。

```
CREATE TABLE comments (comment_id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,user_id INT REFERENCES users(user_id) ON DELETE CASCADE,comment TEXT NOT NULL);
```

在新的 comments 表中，user_id 被定义为一个 INT 引用数据类型，作为外键将 user_id 链接到特定的 comment 实例。ON DELETE CASCADE 告诉 Postgres 删除与特定用户相关联的所有注释(如果该用户已被删除)。这有助于我们保留不再有用的数据。

## 连接表格

现在注释表上有了一个外键，我们可以实现一个连接表。连接表允许我们显示相关数据。它们通过将数据从一个表拖到另一个表来帮助我们遵循单一真实来源的编码原则，而不是手动添加数据，手动添加会导致冲突。

```
SELECTcomment_id, comments.user_id, users.usernameFROMcommentsINNER JOINusersONcomments.user_id = users.user_id;
```

上面的代码将创建一个包含评论 id、用户 id 和用户名的表。它从 comments 表和 users 表中提取这些信息。它们由注释的外键(comments.user_id)连接，该外键与用户的主键(users.user_id)相匹配。

内部连接只是几个连接表中的一个。最常用的连接表是 inner、left 和 right。选择正确的连接表将取决于所需信息的存储位置。下面是对可用的连接表类型的简要描述。

```
INNER: must have a match on both tables, else don’t include it in tableLEFT: include anything from the left table whether it has a match on right tableRIGHT: opposite of leftOUTER: only join things that don’t have matches on either tableFULL OUTER: join everything from both tablesCROSS JOIN: creates every possible combination of joining data from both tables (cartesian product)*careful with CROSS JOIN bc it will likely be a massive amount of data
```

我发现这个[图](https://commons.wikimedia.org/wiki/File:SQL_Joins.svg)对于理解每种类型的连接表之间的差异也很有用。

## 额外资源

我们只接触了 Postgres 的基本功能和特性。我希望这激起了您对 SQL 数据库的好奇心。这里有一些有用的资源，可以帮助你更好地适应这个神奇的工具。

[数据库的完整介绍](https://btholt.github.io/complete-intro-to-databases/)

[Postgres 官方文件](https://www.postgresql.org/)

[免费 SQL 教程](https://www.w3schools.com/sql/)

[PG 管理 GUI](https://www.pgadmin.org/)

[关系数据库讲解](https://www.ibm.com/cloud/learn/relational-databases)