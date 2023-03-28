# PostgreSQL 14 中的 5 个有用特性

> 原文：<https://medium.com/codex/5-useful-features-in-postgresql-14-9e6a7ffd619?source=collection_archive---------0----------------------->

![](img/400bf6882d5a6146059be8375a05d185.png)

PostgreSQL 的新版本最近出来了。一如既往，它带来了一些有趣的更新，其中一些与语法有关，其他的是新功能和性能优化。

让我们来看看 Postgres 14 给我们带来了什么。

另外，如果您有兴趣升级到新版本，您可以简单地这样做:

```
brew services stop postgresql
brew upgrade postgresql
brew postgresql-upgrade-database
brew services start postgresql
```

# **1。无限和-无限**

Postgres 支持各种各样的数字类型。我们可以将它们分类为:

*   **整数类型**
*   **任意精度类型**
*   **浮点类型**
*   **序列类型**

它们由两字节、四字节和八字节浮点数以及可选的精度小数组成。

以下是受支持类型的完整列表:

```
Name       | Storage Size |           Description           
 ------------------|--------------|--------------------------------- 
  smallint         | 2 bytes      | small-range integer             
  integer          | 4 bytes      | typical choice for integer      
  bigint           | 8 bytes      | large-range integer             
  decimal          | variable     | user-specified precision, exact 
  numeric          | variable     | user-specified precision, exact 
  real             | 4 bytes      | variable-precision, inexact     
  double precision | 8 bytes      | variable-precision, inexact     
  smallserial      | 2 bytes      | small autoincrementing integer  
  serial           | 4 bytes      | autoincrementing integer        
  bigserial        | 8 bytes      | large autoincrementing integer
```

除了支持标准数值，从 Postgres 14 开始，它支持**无穷大**和**-in Infinity**。

在 SQL 命令中将这些值作为常量写入时，必须用引号将它们括起来，例如

```
UPDATE table SET x = ‘-Infinity’.
```

在输入时，这些字符串以不区分大小写的方式被识别。无穷大的值也可以拼写为 inf 和-inf。

无穷大值的行为符合数学期望。

```
Infinity + 3 = InfinityInfinity + Infinity = Infinity
```

但是，如果我们这样做:

```
Infinity — Infinity = NaN (not a number)
```

因为它没有明确的解释。请注意，无穷大只能存储在无约束的数字列中，因为它名义上超过了任何有限精度限制。

# **2。JSONB 订阅**

我们知道 Postgres 在相当一段时间内支持 **JSON & JSONB** 。JSONB 数据类型支持数组样式的下标表达式来提取和修改元素。

如果 JSONB 值是一个数组，数字下标从零开始，负整数从数组的最后一个元素开始倒数。不支持切片表达式。订阅表达式的结果总是 JSONB 数据类型。

如果 JSONB 值是一个数组(例如 jsonArray = [ ])，它的行为与许多编程语言中的行为非常相似:

*   **数字订阅从零开始** ( jsonArray[0]可访问)
*   **负整数从数组**的最后一个元素开始倒数(jsonArray[-1]将访问数组的最后一个元素)
*   **切片不受支持**

订阅表达式的结果属于 JSONB 数据类型。

如果一个下标路径对于所有受影响的值都是可遍历的，那么所有这些都将起作用。例如，如果我们想访问

```
jsonArray[‘a’][‘b’][‘c’]
```

如果每个 jsonArray，jsonArray['a']，jsonArray['a']['b']都是一个对象，则路径可以遍历到 *c* 。

如果 jsonArray['a']或 jsonArray['a']['b']未定义，它将被创建为空对象，并在必要时被填充。

如果 jsonArray 本身被定义为一个非对象，比如 string、number of JSONB null，那么就会产生一个错误并中止事务。

只要存在，下标路径对于所有受影响的值都必须是可遍历的。例如，如果每个 val，val['a']，val[' a '][' b '][' c ']都是一个对象，那么路径**可以一直遍历到 c。如果未定义任何 val['a']或 val['a']['b']，它将被创建为空对象，并根据需要进行填充。**

但是，如果任何 val 本身或其中一个中间值被定义为非对象(如字符串、数字或 jsonb null ),遍历将无法继续，因此会引发错误并中止事务。

选择查询的订阅语法示例:

```
— Extract array element by index
**SELECT (‘[1, “2”, null]’::jsonb)[1];**
```

或者，在更新表语法中:

```
— Update object value by key. Note the quotes around ‘1’: the assigned— value must be of the jsonb type as well
**UPDATE table_name SET jsonb_field[‘key’] = ‘1’;**
```

# **3。在触发器上添加或替换**

如果我们看一下触发器函数声明:

```
CREATE [ OR REPLACE ] [ CONSTRAINT ] TRIGGER ***name*** { BEFORE | AFTER | INSTEAD OF } { ***event*** [ OR … ] }ON ***table_name***[ FROM ***referenced_table_name*** ][ NOT DEFERRABLE | [ DEFERRABLE ] [ INITIALLY IMMEDIATE | INITIALLY DEFERRED ] ][ REFERENCING { { OLD | NEW } TABLE [ AS ] ***transition_relation_name*** } [ … ] ][ FOR [ EACH ] { ROW | STATEMENT } ][ WHEN ( ***condition*** ) ]EXECUTE { FUNCTION | PROCEDURE } ***function_name*** ( ***arguments*** )
```

**【或替换】**被添加到触发器中，因此您不必在创建新触发器之前删除触发器。您可以通过使用关键字语法**或 REPLACE** 简单地替换现有的触发器。

看一个基本的例子:

```
CREATE OR REPLACE TRIGGER check_update
BEFORE UPDATE OF balance ON accounts
FOR EACH ROW
EXECUTE FUNCTION check_account_update();
```

# **4。string_to_table()**

他们增加了一个新的函数，可以通过分隔符分割字符串，并将结果作为一组文本行返回。

它最多接受 3 个参数，第一个是您要拆分的字符串，第二个是您要拆分字符串的分隔符，还有第三个可选参数，称为“null_string”，如果提供了该参数，它会用 null 值替换匹配的字符串。

看一下定义:

**string _ to _ table(*string*text， *delimiter* text [，*null _ string*text])→setof text**

```
**string_to_table(‘aa|bb|cc’, ‘|’, ‘bb’)**aa
NULL
cc
```

# **5。PK，UQ 和 FK 到系统目录**

系统编目是 RDBMS(关系数据库管理系统)存储模式元数据的地方。

例如，当您执行**创建** **数据库**时，postgres 会在 **pg_database** 目录中插入一行，并在磁盘上创建数据库。系统目录只是 postgres 用来存储模式**元数据**的常规表(比如关于表和列的信息，以及内部簿记信息)。

您不应该手动更改系统目录。在过去，postgres 函数不能做像现在这样多的事情，所以你可能需要手工做一些事情，但是随着时间的推移，它有了很大的改进。所以，使用函数真的没有什么是你不能做的，而且你特别需要更深入地去做。

总之，您可以删除和重新创建这些表，添加或删除列，插入和更新值..但是这样做可能会严重破坏数据库和系统。

**PostgreSQL 14** 向[系统目录](https://www.postgresql.org/docs/14/catalogs.html)添加了主键、唯一约束和外键。

典型的系统目录表如下所示:

“SELECT * FROM pg_database”的查询结果；`

## 奖励—绩效改进和监控指标

在性能和指标特性方面还有一些额外的改进，例如:

*   **改进的活动和空闲连接扩展**
*   **pg _ back end _ memory _ contexts 的内存使用情况**
*   用 pg_stat_wal 跟踪 **WAL 活动**
*   **监控查询 id 为**的查询
*   新的预定义角色**pg _ read _ all _ data**/**pg _ write _ all _ data**提供全局读取或写入权限