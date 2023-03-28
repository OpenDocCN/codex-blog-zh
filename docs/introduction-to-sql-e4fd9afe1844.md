# SQL 简介

> 原文：<https://medium.com/codex/introduction-to-sql-e4fd9afe1844?source=collection_archive---------12----------------------->

## SQL 是一种数据库语言，允许您存储、操作和检索数据。

![](img/c74be62cda465e59283248e19300dc21.png)

图片来自 [unsplash](https://unsplash.com/photos/fPkvU7RDmCo)

SQL 是一种数据库编程语言，用于检索和管理表数据库中的数据。结构化查询语言(SQL)在事务处理和分析应用程序中用于改变数据库表和索引结构，以及添加、更新和删除数据行和访问数据库中的信息子集。Select、add、insert、update、delete、create、alter 和 truncate 是流行的 SQL 语句。以下是其广泛使用的原因:

*   对于数据专业人员和 SQL 用户来说，在关系数据库中插入、更新和删除数据是 SQL 最基本的用法。
*   数据专业人员和用户可以使用 SQL 从关系数据库管理系统中获取数据。
*   它还有助于结构化数据的描述。
*   SQL 用户可以用它来建立、删除和更新数据库和表。
    它还帮助创建关系数据库视图、存储过程和函数。
*   它使您能够定义和更改关系数据库中包含的数据。
*   SQL 用户还可以为表列、视图和存储过程建立权限和限制。

## 更多文章

 [## Newlearn.blog -一起学习！

### NewLearn 提供技术和商业方面的知识。如果你想了解更多，我们邀请你访问我们，在那里…

newlearn.blog](https://newlearn.blog/) 

当您对 RDBMS 执行 SQL 查询时，系统会确定跟踪请求的最佳方法，而 SQL 引擎会确定要分析的内容。这些是元素:

*   SQL 查询引擎
*   经典查询引擎
*   查询调度程序
*   优化引擎等。

以下是一些最常用的 SQL 命令:

1.  创建命令
2.  选择命令
3.  插入命令
4.  更新命令
5.  丢弃命令
6.  删除命令

# SQL 的优势

SQL 有几个好处，帮助它在数据科学领域越来越受欢迎。对于数据专业人员和用户来说，这是一种与数据库交互的极好的查询语言。SQL 的优势或好处如下:

## 不需要编码。

对于数据库管理，SQL 不需要大量的代码行。使用基本的 SQL 语法规则，我们可以快速访问和维护数据库。

## 高速查询处理

SQL 查询用于快速有效地从数据库中获取大量数据。插入、删除和更新等数据活动花费的时间更少。

## 语言是有定义的

SQL 遵循 ISO 和 ANSI 标准，这些标准为它在世界各地的所有用户提供了一个一致的平台。

## 灵活性

台式电脑、笔记本电脑、平板电脑甚至智能手机都可能使用结构化查询语言。根据用户的需要，它还可以与其他程序配合使用。

# SQL 的缺点

它也有如下缺点:

## 价格

一些 SQL 版本的运营成本很高。因此，一些开发人员不使用 SQL。

## 用户界面很复杂

另一个显著的缺点是结构化查询语言接口复杂，使得 SQL 用户难以使用和管理。

## 数据库控制其部分

商业规则是保密的。结果，使用这种查询语言的数据专业人员和用户不能完全控制数据库。

# SQL 创建数据库命令

```
CREATE DATABASE database_name;
```

# SQL 插入命令

```
INSERT INTO table_name( column1, column2....columnN)
VALUES ( value1, value2....valueN);
```

# SQL 截断表命令

```
TRUNCATE TABLE table_name;
```

# SQL ALTER TABLE 命令

```
ALTER TABLE table_name {ADD|DROP|MODIFY} column_name {data_ype};
```

# SQL ALTER TABLE 命令(重命名)

```
ALTER TABLE table_name RENAME TO new_table_name;
```

# SQL 更新命令

```
UPDATE table_name
SET column1 = value1, column2 = value2....columnN=valueN
[ WHERE  CONDITION ];
```

# SQL 删除命令

```
DELETE FROM table_name
WHERE  {CONDITION};
```

# SQL 删除数据库命令

```
DROP DATABASE database_name;
```

# SQL 删除表命令

```
DROP TABLE table_name;
```

# SQL 选择命令

```
SELECT column1, column2....columnN
FROM   table_name;
```

# SQL DISTINCT 子句

```
SELECT DISTINCT column1, column2....columnN
FROM   table_name;
```

# SQL WHERE 子句

```
SELECT column1, column2....columnN
FROM   table_name
WHERE  CONDITION;
```

# SQL 和/或子句

```
SELECT column1, column2....columnN
FROM   table_name
WHERE  CONDITION-1 {AND|OR} CONDITION-2;
```

# SQL IN 子句

```
SELECT column1, column2....columnN
FROM   table_name
WHERE  column_name IN (val-1, val-2,...val-N);
```

# SQL BETWEEN 子句

```
SELECT column1, column2....columnN
FROM   table_name
WHERE  column_name BETWEEN val-1 AND val-2;
```

# SQL LIKE 子句

```
SELECT column1, column2....columnN
FROM   table_name
WHERE  column_name LIKE { PATTERN };
```

# SQL ORDER BY 子句

```
SELECT column1, column2....columnN
FROM   table_name
WHERE  CONDITION
ORDER BY column_name {ASC|DESC};
```

# SQL GROUP BY 子句

```
SELECT SUM(column_name)
FROM   table_name
WHERE  CONDITION
GROUP BY column_name;
```

# SQL 计数子句

```
SELECT COUNT(column_name)
FROM   table_name
WHERE  CONDITION;
```

# SQL HAVING 子句

```
SELECT SUM(column_name)
FROM   table_name
WHERE  CONDITION
GROUP BY column_name
HAVING (arithematic function condition);
```

# SQL 创建表命令

```
CREATE TABLE table_name(
column1 datatype,
column2 datatype,
column3 datatype,
.....
columnN datatype,
PRIMARY KEY( one or more columns )
);
```

# SQL 创建唯一索引命令

```
CREATE UNIQUE INDEX index_name
ON table_name ( column1, column2,...columnN);
```

# SQL 删除索引命令

```
ALTER TABLE table_name
DROP INDEX index_name;
```

# SQL DESC 命令

```
DESC table_name;
```