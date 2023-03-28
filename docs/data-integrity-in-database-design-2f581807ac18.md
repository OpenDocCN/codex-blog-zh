# 数据库设计中的数据完整性

> 原文：<https://medium.com/codex/data-integrity-in-database-design-2f581807ac18?source=collection_archive---------12----------------------->

## 基础知识，使用 SQLite

![](img/0bab5fa2d5c5750a75295e7932093c8d.png)

詹姆斯·哈里逊在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

动手操作 SQL 项目很有趣，但学习数据库设计背后的理论和概念也很有趣。今天，我将简单地谈谈数据完整性，以及我在学习 SQL 的初学者之旅中所学到的东西。

今天的帖子将基于下面的 YouTube 视频，我强烈建议你先去看看:

我是凯莱布·库里的超级粉丝，因为他用直观、日常用语和超级简单易懂的方式打破了所有这些概念。他只是让学习数据库设计变得有趣，这篇文章将主要是我自己基于他的这一课的笔记(加上我自己的一些进一步研究)。对于未来关于基本数据库设计概念的帖子，我可能会以他的视频为基础来写。

不管怎样，我们继续吧。

数据完整性基本上意味着确保数据库设计中的数据是“正确的”，并且表之间的关系是完整的。通常分为三种类型:

1.  **实体完整性:**每行必须唯一标识。例如，在一个 *users* 表中，您经常会遇到两个或更多的人有相同的名字(甚至是姓氏)，为了确保每一行都被唯一地标识，您必须包含一些唯一的标识符，使行作为一个整体是唯一的。在 SQLite 中，一种方法是添加一个 ID 列，并将其设置为主键
2.  **参照完整性:**对于相互之间有关系的表，您必须确保这种特定的关系被明确定义。回到我们的用户表示例，如果我们有另一个表来存储用户评论，那么每一行(即每条评论)都必须指向用户表中存在的特定用户。在 SQLite 中，我们可以通过使用外键约束来实现这一点，例如在 *comments* 表中包含一个 *user_id* 列，该列指向 *users* 表中的一个特定的 *user_id* 。
3.  **域完整性:**每一列中的值必须被定义并保持一致。我们不想要空值，也不想要，比方说，电话号码列中的字符串值。在 SQLite 中，我们可以使用 CHECK 和 NOT NULL 来实现这一点。

让我们用一个简单的例子来演示以上内容，让我们实际制作*用户*和*用户评论*表。*用户*表将包含用户 id、用户名和电话号码信息，而*用户评论*将包含评论 id、评论本身以及与该评论相关联的用户(作为指向*用户*表的外键):

```
CREATE TABLE users (user_id INTEGER PRIMARY KEY AUTOINCREMENT, 
 user_name TEXT, 
 phone_number INTEGER);CREATE TABLE userComments (
 comment_id INTEGER PRIMARY KEY AUTOINCREMENT,
 comment TEXT,
 user_id INTEGER,
 FOREIGN KEY (user_id) REFERENCES users (user_id)
 );
```

在我看来很简单。现在让我们填充这两个表:

```
INSERT INTO users (user_name, phone_number) VALUES ("Bob", 12345678900);
INSERT INTO userComments (comment, user_id) VALUES ("I like this video!", 1);INSERT INTO users (user_name, phone_number) VALUES ("Joe", 12345678912);
INSERT INTO userComments (comment, user_id) VALUES ("Me too!", 2);
```

让我们通过对两个表进行简单的选择来快速检查，并得出以下结果:

```
-- SELECT * FROM users;
1|Bob|12345678900
2|Joe|12345678912-- SELECT * FROM userComments;
1|I like this video!|1
2|Me too!|2
```

在这里，我们看到了上述三种完整性的元素。我们有实体完整性，因为每个用户和评论都由某个 user_id 和 comment_id 标识；我们有引用完整性，因为 *userComments* 表中的每一行都指向 *users* 表中的一个现有用户；我们有域完整性(目前而言),因为我们在定义文本值和整数值的地方有它们。

酷，让我们试试:

```
INSERT INTO users (user_name, phone_number) VALUES (123456, "Hi, My name is Peter!");INSERT INTO userComments (comment, user_id) VALUES (246810, 3);
```

还有…..成功了。事实上，我们可以在两个表上再次调用 SELECT 并看到它:

```
-- SELECT * FROM users;
1|Bob|12345678900
2|Joe|12345678912
3|123456|Hi, My name is Peter!-- SELECT * FROM userComments;
1|I like this video!|1
2|Me too!|2
3|246810|3
```

*为什么？*

如果你想进一步阅读，我会留下下面的链接，但基本上 SQLite 将这视为一个功能，而不是一个错误，具体称为*动态类型*，这基本上意味着你可以将任何数据类型插入任何列，即使你在列中分配了一个。它还具有*型亲和力。*例如，如果你声明一个列为 INT 并插入文本，在插入到你的表中之前，它将首先尝试转换为 INT，否则如果它不能这样做，它将存储为文本，或者至少我是这样理解的。

那么，我们如何在我们的列中实施我们的数据类型呢？我们可以使用检查约束来做到这一点。基于同一个示例，我想使用以下约束重新创建表:

*   用户名只能是 3 个字母
*   电话号码必须是数字，且不超过 11 位
*   注释必须≤ 20 个字符

让我们将其转换成 SQL:

```
CREATE TABLE users (user_id INTEGER PRIMARY KEY AUTOINCREMENT, 
 user_name TEXT CHECK (LENGTH(user_name) <= 3), 
 phone_number INTEGER CHECK (phone_number BETWEEN 0 AND 99999999999));CREATE TABLE userComments (
 comment_id INTEGER PRIMARY KEY AUTOINCREMENT,
 comment TEXT CHECK (LENGTH(comment) <= 20),
 user_id INTEGER,
 FOREIGN KEY (user_id) REFERENCES users (user_id)
```

并插入一些数据(第三个不遵循定义的约束):

```
INSERT INTO users_two (user_name, phone_number) VALUES ("Bob", 12345678900);
INSERT INTO userComments_two (comment, user_id) VALUES ("I like this video!", 1);INSERT INTO users_two (user_name, phone_number) VALUES ("Joe", 12345678912);
INSERT INTO userComments_two (comment, user_id) VALUES ("Me too!", 2);INSERT INTO users_two (user_name, phone_number) VALUES (123456, "Hi, My name is Peter!");
INSERT INTO userComments_two (comment, user_id) VALUES ("I like apple pies and nuts and rice", 3);
```

这一次，您将得到如下类似的错误:

```
Error: near line 43: CHECK constraint failed: LENGTH(user_name) <= 3
Error: near line 44: CHECK constraint failed: LENGTH(comment) <= 20
```

如果您选择两个表，您将得到:

```
-- SELECT * FROM users_two;
1|Bob|12345678900
2|Joe|12345678912-- SELECT * FROM userComments_two;
1|I like this video!|1
2|Me too!|2
```

这意味着两个表的第三行条目都无法插入。

我的帖子到此结束，希望对你也有帮助！

使用的参考:

[](https://www.tutlane.com/tutorial/sqlite/sqlite-constraints) [## SQLite 约束- Tutlane

### 在这里，我们将通过示例学习 sqlite 约束和不同类型的 SQLite 约束(主键、外键…

www.tutlane.com](https://www.tutlane.com/tutorial/sqlite/sqlite-constraints) [](https://www.sqlite.org/faq.html#q3) [## SQLite 常见问题

### 小。很快。可靠。任选三个。如何创建自动增量字段？SQLite 支持哪些数据类型…

www.sqlite.org](https://www.sqlite.org/faq.html#q3) [](https://www.sqlite.org/datatype3.html#affinity) [## SQLite 中的数据类型

### 大多数 SQL 数据库引擎(据我们所知，除了 SQLite 之外的所有 SQL 数据库引擎)都使用静态的、严格的类型…

www.sqlite.org](https://www.sqlite.org/datatype3.html#affinity)