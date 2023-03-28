# 如何自学 SQL

> 原文：<https://medium.com/codex/how-to-learn-sql-by-yourself-877a8f8d7717?source=collection_archive---------5----------------------->

## 相信我，当我说*任何人都可以学习 SQL* 。

![](img/5943b691ca5f54b7c81b928a4b9cb943.png)

JESHOOTS.COM 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[的照片](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)

我知道这听起来像是我在用奥古斯特·古斯特的名言*—*任何人都可以烹饪*。但我不是在开玩笑。我是认真的。如果你想成为一名数据/业务分析师，甚至是一名数据科学家，SQL 是你能学到的最简单的东西。*

*在学习 SQL 之前，我对任何编程语言或数据库都没有任何经验。我以阿达的身份开始分析 Google sheets 中的数据，当数据转储请求对数据科学团队来说变得过于频繁时，他们愿意向我提供对 DB 的只读访问(在我们的例子中是 redshift)。我记得按照 AWS 支持文章在我的系统上设置 SQL 工作台。安装完成后，我尝试了非常基本的 SQL 语句，比如简单的 Select 语句。一开始，我经常不得不在 w3schools 这样的网站上查找语法。*

*我学到的方法是我一头扎进去，没有做任何 MOOC 或教程。我向数据科学团队要了几个示例查询，并开始改变一些东西，看看什么改变会影响数据的什么部分。一旦我熟悉了查询的基本语法，我就尝试自己为手头的问题编写查询。如果卡住了， [*AWS 文档*](https://docs.aws.amazon.com/redshift/latest/dg/c_SQL_commands.html) 是我的第一站。接下来，我试着在 [*栈溢出*](https://stackoverflow.com/) 上寻找答案，有时问几个问题来清除我的疑惑。*

*我甚至学习了窗口函数，仅仅是因为我需要得到我正在做的事情的结果，甚至没有意识到它被认为是高级 SQL。我从未意识到我对 SQL 的熟悉和熟练，直到有一天我开始接到面试电话，出于好奇，我很少出去。我通过了包括亚马逊在内的所有公司的 SQL 轮面试。作为一名自学 SQL 的学习者，这是一个值得骄傲的时刻。*

*如果您无法访问数据库，您也可以用示例数据建立自己的数据库，并开始探索它。您可以使用 W3Schools 获得 SQL 命令来设置一个。选择一个问题陈述和数据。将数据添加到您在本地机器上设置的数据库中。你可以从按照这个顺序写基本陈述开始-*

1.  ***使用各种过滤器检索数据** —这将帮助您学习如何使用 where 子句以及 and、or、not 运算符。*
2.  ***聚合数据—** 这将帮助您学习如何使用 Count()、Sum()、Min()、Max()、Average()等函数以及 group by 和 order by 子句。*
3.  ***表格连接—** 不要害怕连接。它们就像古老的集合论一样工作，对于编写高效的查询非常重要。所以，要特别注意他们。*
4.  ***窗口函数** —窗口函数有助于使计算更容易、更快。它们可以让你计算诸如——滚动总和、滚动平均值、中间值、前一天和当天值之间的变化等。几个函数是— sum()、median()、min()、max()、lead()、lag()、list_agg()、rank()、last_value()、first_value()。*
5.  ***正则表达式** —正则表达式帮助你进行模式匹配。它们既可以用在 Select 子句中(使用 Case 语句)，也可以用在 where 子句中(使用类似于关键字的*)。**
6.  ***On the go —** 还有很多其他的函数和表达式，比如 having，in，between，distinct，split_part()，trim()，lower()，upper()，substring()，reg_replace()等等。学习的机会是无穷无尽的。*

> *总而言之，如果你能访问数据库，任何种类的数据库，那就直接进入吧。尝试各种语法，看看神奇的事情发生了。如果你无法访问数据库，那么有很多在线平台可以让你练习 SQL，比如 H [ackerrank](https://www.hackerrank.com/domains/sql) 、 [W3Schools](https://www.w3schools.com/sql/) 、 [Datacamp](https://www.datacamp.com/courses/introduction-to-sql) 。*
> 
> *只要开始练习，你练习得越多，你就会做得越好。*

*我希望您能从这篇文章中找到一些动力，现在 SQL 不再像以前那样让您感到害怕了。*

*感谢阅读。*

*快乐学习！*