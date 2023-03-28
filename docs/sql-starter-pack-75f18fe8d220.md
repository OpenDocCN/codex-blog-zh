# SQL 初学者包

> 原文：<https://medium.com/codex/sql-starter-pack-75f18fe8d220?source=collection_archive---------7----------------------->

![](img/f1f880ed1439d6bcf23a19c14e418673.png)

如果您希望过渡到数据科学和分析，SQL 将是您应该首先学习的内容之一。与其他工具包相比，除了相对容易的学习曲线之外，精通 SQL 也将为你找工作打开大门。根据经验，无论你是在寻找一家科技公司还是非科技公司，SQL 都将是一项方便的技能，因为它是他们“基本要求”的一部分。至少在菲律宾，大多数公司仍处于数字化转型过程中，SQL 帮助他们的分析师提取和管理他们的数据库。

SQL，即结构化查询语言，通常用于查询和分析数据存储或称为关系数据库的表集合。这对于数据分析师非常有用，但对于管理数据的数据科学家也很有用。

SQL 中最常用的语句是 SELECT、FROM、WHERE、GROUP BY、HAVING 和 ORDER BY。SELECT 和 FROM 是查询中的**强制**语句，而其他语句是可选的，但是应该按照特定的顺序使用。为了更容易记住，我的朋友 [@janinecheong](http://twitter.com/janinecheong) 创造了一个助记符——“三藩市，我们要吃辣奥利奥了！”

**从**中选择&

*   选择:用于通过指定要反映的列从表中选择数据
*   要从表中选择多列，请使用逗号(，)分隔不同的列
*   要选择表中的所有列，请使用星号(*)
*   若要仅选择所有唯一值并忽略重复值，请使用 DISTINCT 关键字
*   FROM:指定从中提取数据的表

```
-- selecting NAME and ADDRESS columns from PEOPLE table
**SELECT** name, address
**FROM** people;-- selecting ALL columns from PEOPLE table
**SELECT** *
**FROM** people;-- selecting unique values from NAME column from PEOPLE table
**SELECT DISTINCT** name
**FROM** people
```

COUNT 语句可用于补充 SELECT 语句，以返回特定列中非缺失值/行的数量。对数据进行探索性分析时，COUNT 可以与 DISTINCT 一起使用，以确定列中有多少个唯一值。

```
-- selecting the count of values from TRANSACTION_ID column from ORDERS table **
SELECT** COUNT(transaction_id)
**FROM** orders;-- selecting the count of UNIQUE values from EMPLOYEE_ID column from EMPLOYEE table **SELECT** COUNT(DISTINCT employee_id)
**FROM** employee;
```

LIMIT 关键字可用于在结果中仅返回特定数量的行。如果您只想验证每列中应该有什么类型的值，这很有用。

```
-- selecting the FIRST FIVE values of ALL columns from PEOPLE table **SELECT** *
**FROM** people
**LIMIT** 5;
```

使用 AS 关键字的别名可以用来重命名选定的列。

```
-- renaming the derived count of values from TRANSACTION_ID column as num_of_transactions **SELECT** COUNT(transaction_id) **AS** num_of_transactions
**FROM** orders;
```

**其中**

*   设置过滤数据时要遵循的参数或逻辑条件
*   以下运算符可用于设置过滤逻辑

```
+ — — — — + — — — — — — — — — — — — — — — -+
| =       | equals                         |
| <>      | does NOT equal                 |
| <       | greater than                   |
| >       | less than                      |
| <=      | greater than or equal to       |
| >=      | less than or equal to          |
| BETWEEN | range between two values       |
| LIKE    | match with similar pattern     |
| IN()    | equals one of specified values |
+ — — — — + — — — — — — — — — — — — — — — -+
```

WHERE 语句的使用示例

```
-- selecting values of ALL columns from EMPLOYEE table, given that the employee is hired on the year of 2016 or later
**SELECT** *
**FROM** employee
**WHERE** hired_year >= 2016;-- return the COUNT of employees from the EMPLOYEE table, hired on December 1, 2020
-- IMPORTANT: remember to use ISO date format (YYYY-MM-DD) **SELECT** **COUNT**(*)
**FROM** employee
**WHERE** hired_date = '2020-12-01';
```

诸如 AND、OR、IN 和 LIKE 之类的附加语句可以与 WHERE 一起使用，以修改过滤结果

```
+ — — -+ — — — — — — — — — — — — — — — — — — — — — — — — — — — -+
| AND  | returns records meeting ALL of multiple conditions set |
| OR   | returns records meeting ANY of multiple conditions set |
| IN   | alternative to using multiple OR conditions            |
| LIKE | use of wildcard or pattern matching as conditions      |
+ — — -+ — — — — — — — — — — — — — — — — — — — — — — — — — — — -+
```

使用 AND & OR 语句的例子。

```
-- selecting title of songs by ‘The Chainsmokers’ and ‘Custismith’ from PLAYLIST table
-- if filtering for two values under the same column name, the column name should be repeated when filtering
**SELECT** title
**FROM** playlist
**WHERE** artist = ‘The Chainsmokers'
**AND** artist = ‘Curtismith’;-- selecting title and release_year of all KPOP songs after 2020
**SELECT** title, release_year
**FROM** playlist
**WHERE** release_year > 2020
**AND** language = 'Korean'; -- selecting songs released in either 2010 or 2020
-- if filtering for two values under the same column name, the column name should be repeated when filtering
**SELECT** title
**FROM** playlist
**WHERE** release_year = 2010
**OR** release_year = 2020;-- when combining AND and OR, be sure to enclose the individual clauses in parenthesis
**SELECT** title
**FROM** playlist
**WHERE** (genre = 'pop' OR genre = 'country')
**AND** (artist= 'Taylor Swift' OR artist= 'Blake Shelton');
```

**分组依据**

*   设置如何对数据/结果进行分组的标准
*   数据可以由多列分割，这将允许用户创建一个类似的子组数据透视表视图

```
-- counting the number of days when overtime was rendered by employees, grouping by job grade
**SELECT** job_grade, **COUNT**(num_of_overtime_date)
**FROM** employee
**GROUP** BY job_grade-- multiple groupings
**SELECT** category, rating, COUNT(title) as count_of_movies
**FROM** film_list
**GROUP** BY category, rating
```

COUNT、COUNT DISTINCT、MIN、MAX、AVG 和 SUM 等聚合函数可用于提供组级别的统计数据。

```
+ — — — — — — — -+ — — — — — — — — — — — — — — — — -+
| COUNT          | count of records                 |
| COUNT DISTINCT | count of distinct values         |
| MIN            | returns the lowest value         |
| MAX            | returns the highest value        |
| AVG            | average of all values            |
| SUM            | adds up the total numeric values |
+ — — — — — — — -+ — — — — — — — — — — — — — — — — -+
```

使用聚合函数和 GROUP BY 的示例。

```
-- create a table to compare rental price of title per film rating
**SELECT**
     rating,
     **COUNT**(title) AS num_of_films,
     **MIN**(price) AS cheapest_rental,
     **MAX**(price) AS most_expensive_rental
     **AVG**(price) AS average_rental
**FROM** film_list
**GROUP** BY rating
```

**有**

*   基于聚合函数结果的过滤器

```
-- returns the list of ratings with at least 20 films in its category
**SELECT**
    rating,
    **COUNT**(*) AS num_of_films
**FROM** film_list
**GROUP BY** rating
**HAVING COUNT(*)** >= 20
```

**排序依据**

*   用于对结果进行排序，默认情况下按升序排列
*   DESC 可用于按降序对值进行排序
*   可用于多个条件，但第一个条件将优先，然后按下一个排序，很快。

```
-- select list of employees showing their employee_id, job_grade, and hired year in order of newest hire to oldest hire
**SELECT** employee_id, job_grade, hired_year
**FROM** employee
**ORDER BY** hired_year, hired_date DESC
```

**升级您的 SQL 之旅**

练习上面的语句将是一个很好的开始来扩展你的基本 SQL 知识。如果你想追求，下面的概念将有助于巩固你的基础。

*   什么是关系数据库管理系统(RDBS)，它是如何工作的？
*   使用联接(内联接、左联接、右联接、全外联接、联合联接)
*   没有公共字段的桥接表

以下是有用的参考:

*   【https://www.w3schools.com/sql/ 
*   [https://www.udemy.com/course/mysql-for-data-analysis/](https://www.udemy.com/course/mysql-for-data-analysis/)

如果你喜欢阅读这份快速参考指南，并想了解更多，请随时通过 [Twitter](https://twitter.com/maicorebong) 或 [LinkedIn](http://linkedin.com/in/maico-r-919389193) 联系我。我将非常高兴听到你的学习之旅！