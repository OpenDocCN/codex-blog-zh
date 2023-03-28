# 熊猫能承担 SQL 吗？

> 原文：<https://medium.com/codex/can-pandas-take-on-sql-7fd6dedca5a5?source=collection_archive---------12----------------------->

## 谁会赢？

![](img/d0030843adaf7442d6f3f7b00406296c.png)

SQL 已经存在很多年了，它是一个受欢迎的选择，因为它易于学习和理解。后来来了*熊猫。*今天我们开始比较这两个。

***那么，没有任何进一步的到期让我们开始吧！！***

*SQL* 和 *Python* 是现在现实世界中数据分析师和科学家必备的两种语言。当任何人开始探索数据分析时，他们通常首先从 *SQL* 开始，然后慢慢转向 *Python* 。

我们将尝试比较一下 *SQL* 和 *Pandas* 中的一些非常重要的命令。 *SQL* 是一种用于在关系数据库中存储、操作和检索数据的语言。Pandas 是 python 中的一个库，用于数据分析和操作。

# 导入数据

# 结构化查询语言

在 SQL 中导入数据比在 Pandas 中要难一些。我们必须首先使用 **CREATE TABLE** 语句创建一个表，其字段类似于文件中提到的字段，然后从文件中复制数据。检查下面的代码:

```
CREATE TABLE dataset_name (
    column1 character data_type(30),
    column2 character data_type(50),
    column3 data_type,
);copy fert_data from ‘Path\to\file’ with delimiter ‘,’ csv header encoding ‘windows-1251’;
```

# 熊猫

现在将上面的工作与*熊猫*中的这段代码进行比较，用于导入文件。

```
df = pd.read_csv(’Path\to\your\dataset’)
```

就这样，您已经成功导入了所有文件🎊

# 挑选

# 结构化查询语言

在 *SQL* 中，我们使用一个 **SELECT** 语句从表中获取记录。这种说法应该是:

```
SELECT column_name1, column_name2,…
FROM table_name;
```

如果你的数据集中有一百万条记录会怎样…，现在你可能会犹豫这样看它们，以避免我们使用 **LIMIT()** 并传递一个整数值给它

在熊猫中，我们用 **head()** 和 **tail()** 分别取前 5 行和后 5 行。这在字面上等于 *SQL* 中的`SELECT * FROM table LIMIT 5`。如果必须从数据框中选择特定的列，则语法应为:

```
df[[‘column_name1’, ‘column_name2’]]
```

# 在哪里

# 结构化查询语言

在 *SQL* 中，一个 **WHERE** 子句用于根据条件过滤记录。子句通常与条件和逻辑运算符结合使用，以形成过滤条件。语法应该是:

```
SELECT *
FROM table_name
WHERE condition_expression;
```

# 熊猫

Pandas 中没有像 *SQL* 这样的过滤记录的单一命令。我们可以使用布尔索引和位置索引。在*中，熊猫*过滤是通过逻辑条件计算的。其语法应该是:

```
df[df[‘column_name’] == value] # for Boolean indexingdf[df.loc[‘column_name’] == value] # for positional indexing
```

在 *SQL* 和*熊猫*中，我们都可以使用比较(>、<、> =、< =、！=、==)和逻辑运算符(' and '、' or '、' not '或符号，如' | '、'【T57'、' ~ ')

# 插入

# 结构化查询语言

**Insert** 语句用于在数据库表中插入行。语法应该是:

```
INSERT INTO table_name(column_name_1, column_name_2,…) VALUES (value1, value2,…);
```

# 熊猫

在*熊猫*中，没有在数据框中插入新行的特定功能。我们可以使用诸如 **concat()** 或 **append()** 这样的工具来执行这个任务。但是最常用的是 **append()** 。

```
df = df.append(new_row_to_be_inserted, ignore_index=True)
```

# 更新

# 结构化查询语言

在 *SQL* 中， **UPDATE** 语句用于更新或修改现有记录的值。update 语句的语法是:

```
UPDATE table_name
SET column_name = ‘modified_value’
WHERE condition;
```

# 熊猫

在 *Pandas* 中，没有更新现有行的功能。一个常见的程序是通过使用我们之前在**过滤器**的比较中讨论的位置索引来定位应该更新或修改的位置，然后为其分配一个新值。这几乎就像给一个变量赋值。

# 结论

如你所见，SQL 和熊猫 T21 在某些方面都更好，没有明显的赢家。对于一些任务，比如插入和更新， *SQL* 是一个支配者，因为它的代码可读性更强，更容易理解，而 *Pandas* 不需要你预先为数据创建一个数据结构，这可以节省大量的时间。想象一下，创建一个包含 50 多列数据的表，或者将 50 多列数据分成多个表，并使用连接，这真的很痛苦。

别忘了留下你的回答。✌

大家敬请关注！！为了把我的故事发到你的邮箱里，请订阅我的时事通讯。感谢您的阅读！不要忘记鼓掌，分享你的回答，并与朋友分享。