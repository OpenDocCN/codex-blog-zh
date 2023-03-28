# 在 Rails 中使用 ActiveRecord 作用域方法:如何和为什么

> 原文：<https://medium.com/codex/using-activerecord-scope-methods-in-rails-how-and-why-36fa99972561?source=collection_archive---------8----------------------->

Rails 中的范围方法是由 ActiveRecord 查询接口启用的特殊类方法，它允许程序员编写 SQL 查询，而无需钻研完整的 SQL。

> 为什么开发人员想要使用范围方法或范围？

范围是**快**。

与用 Ruby 编写的类方法相比，作用域方法快得令人难以置信。他们可以在数据库上执行查询，并在很短的时间内返回所需的结果。虽然这在开发阶段可能不会产生影响，但是当应用程序达到一定规模时，它肯定会产生影响。在设计过程的早期考虑可伸缩性允许我们消除一些我们作为开发人员必须执行的重构。

瞄准镜高度**可定制**。

因为作用域方法可以包含 SQL 代码片段，所以它们可以在一行代码中对数据执行复杂的操作，而在 Ruby 中需要几十行代码才能完成相同的逻辑。像所有的类方法一样，作用域也可以被链接。它们高度模块化，非常适合复杂的数据操作。

范围是**灵活**。

因为作用域利用了 ActiveRecord 查询接口，所以它们与大多数数据库系统兼容，包括 MySQL、MariaDB、PostgreSQL 和 SQLite。使用不同形式的基于 SQL 的数据存储不需要改变作用域——代码每次都是相同的。

> 范围是怎么写的？

范围几乎有无限多种，但是有一些重要的特性需要注意。首先，作用域是类作用域的方法。在特定的实例中有多种工作方式，但是作用域操作的是一个类，也就是一个数据表。

基本格式是:

```
scope :name_of_method, -> { ActiveRecord query verb(attribute: value)... }
```

下面是作用域可以采用的最简单的形式:

```
scope :public_users, -> { where(public: true) }
```

这个作用域作用于类 User，并要求所有公共属性(boolean)为 true 的用户。在控制台中，这可以通过键入

```
User.where(public: true)
```

编写作用域时，在控制台中一点一点地解决它们通常很有帮助。在将两个语句链接在一起的示例中，有一个稍微复杂一些的范围:

```
scope :order_by_due_date_and_priority, -> { order(:due_date).order(:priority) }
```

下一个复杂级别可能是向我们的 scope 方法添加一个参数。其格式为

```
scope :name_of_method, -> (argument) { ActiveRecord verb(attribute: argument) }
```

最简单的例子是

```
scope :category_tasks, ->(cat) { where(category: cat) }
```

此范围要求特定类别的所有任务。只需选择一个类别并键入，就可以在控制台中对其进行测试(至少是部分测试)

```
Task.where(category: "Future")
```

以上介绍了编写带参数和不带参数的作用域的基础知识。为了展示作用域的威力，这里有一些更复杂、更有用的例子来说明如何编写作用域。

```
scope :next_five_tasks, ->(user) { where(user_id: user.id).where("due_date > ?", Date.today).where("category != ?", "Complete").order(:due_date).limit(5)}
```

在这个作用域中，采用 user 参数，以便只从该用户对象中提取任务。接下来，due_date 属性限于当前日期之后的日期。数据也受到限制，以便不显示标记为“完成”的任务然后按照 due_date 对任务进行排序(除非另有标记，否则将按升序排序),并且数量限制为五个。这一行代码的结果是能够在用户页面上生成以下列表:

![](img/263bbd0cda4efad89ef500b71fd9d5c1.png)

在编写作用域时，为了达到预期的结果，ActiveRecord 谓词需要按特定的顺序进行链接。最好的建议是测试每一部分，慢慢缩小方法的范围，直到只产生所需的数据。这可以通过结合终端使用和网站显示来实现。

有时，需要覆盖 ActiveRecord 的假设来检索正确的数据。例如，使用“joins”方法，ActiveRecord 将在每个表的 id 变量上假设一个内部连接。但是，有时这并不理想，因此开发人员可以插入一些 SQL 来定制 ActiveRecord 查询:

```
scope :public_projects, -> { joins('INNER JOIN users ON projects.owner = users.id').where('users.public = true')}
```

这个范围写在类项目中。在这种情况下，开发人员希望将项目表的“所有者”列连接到用户表的“id”列，而不是将项目表和用户表连接到两个 id 列。然后，缩小数据范围，只包括标记为公共的用户。因此，此范围检索到的所有项目都是由公共用户创建的，并且可以接受公共查看。

# 结论

当开发人员需要代码与数据库进行交互并提取具有特定属性的数据时，作用域通常是最佳选择。尽管由于它们的结构和仅限于类的范围，它们可能很难学习，但是这些方法非常快速、可定制和灵活。它们比类方法更干净、更简洁，并改善了应用程序的外观和可伸缩性。

*资源*

*   [ActiveRecord 查询接口，Ruby on Rails 指南](https://guides.rubyonrails.org/active_record_querying.html)
*   [如何在 Ruby on Rails 中使用作用域，RubyGuides](https://www.rubyguides.com/2019/10/scopes-in-ruby-on-rails/)