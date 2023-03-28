# 实体框架的最佳实践

> 原文：<https://medium.com/codex/best-practices-with-entity-framework-567ac9d5493a?source=collection_archive---------0----------------------->

实体框架为开发人员提供了很多便利，但在某些情况下，也会给用户带来性能损失。开发人员的工作就是充分利用实体框架，同时保持良好的用户体验。

![](img/a077b95af347c3d9e961a64d40cea201.png)

我曾经很长一段时间使用 SQL 查询编写代码。但是在转移到实体框架之后，我无法想象回去。模型对象中整个数据库表的抽象使得管理数据库和推出更新变得非常容易，而不用担心破坏任何功能。

如果你像我一样，喜欢将大多数数据库操作放在应用程序代码中，而不是放在数据库中(存储过程、触发器等)。)，你可能也遇到过实体框架的性能问题。根据使用情况，在大多数情况下，性能损失可以忽略不计。例如，如果您插入、更新或删除单个记录，这种差异通常不明显。但是，在执行批量操作时，实体框架可能会导致严重的延迟。

这些年来，我学到了一些避免性能损失的最佳实践，同时仍然保持实体框架的能力。

# 永远不要使用 RemoveRange！

有时，您需要根据条件删除多条记录，EntityFramework 提供的默认方法是 RemoveRange。

```
db.Table.RemoveRange(db.Table.Where(a => a.Price <= 0).ToList())
```

上述方法的问题是，它为底层查询返回的每一条记录触发一个删除查询。例如，如果查询`db.Table.Where(a => a.Price <= 0)` 产生 20 条记录，那么实体框架将向数据库触发 20 次删除查询。这是非常低效的，应该完全避免(如果数字是 20 可能还可以，但是到了 2000 就不行了！).有一个名为*Z . entity framework . extensions*的 ***Nuget*** 包，使得这个更加健壮。使用这个扩展包，您可以编写如下查询:

```
db.Table.Where(a => a.Price <= 0).DeleteFromQuery()
```

这个扩展有一个免费版本，支持 DeleteFromQuery 和许多其他方法。要安装免费的 ***Nuget*** ，在 Nuget 包管理器中搜索 *z.entity* 。

# 不要在 DbContext 实例中循环！

通常，当您启动 DbContext 实例时，它看起来像这样:

```
using (AppDbContext db = new AppDbContext())
{
   // your code here
}
```

在这个实例中放一个 for/while 循环从来都不是一个好主意，如果循环中的*有一些 Db 操作正在进行。基本上发生的是，每次迭代，实体框架保持跟踪实体(即使你在循环中调用 SaveChanges)。随着迭代次数的增加，运算时间会不断增加。如果您的循环迭代 1000 次，您的第 1000 次迭代时间可能会比第一次迭代多 10000 倍(这不是经过测试或测量的数字，只是指示影响的粗略估计)。*

*因此，最好在循环中实例化 DbContext 的新实例:*

```
*foreach(var x in items)
{
   using (AppDbContext db = new AppDbContext())
   {
       // your code here
   }
}*
```

***。NET Core users** :如果您使用 DbContext 作为依赖注入，那么避免使用 DbContext 实例进行批量/循环操作可能是个好主意。创建 DbContext 类的另一个构造函数，您可以使用上面显示的 语句用 ***直接调用它。****

# *仅在必要时使用 ExecuteSqlCommand*

*ExecuteSqlCommand 相当于对数据库执行原始 SQL 查询，其中实体框架不执行任何实体跟踪/验证。*

*但是，在某些情况下，您可能无法避免使用它。例如，您可能需要归档表中超过 6 个月的记录。在这种情况下，运行单个查询并更新相关的列比在 EF 中提取记录并逐个更新更容易。一个例子可能是:*

```
*using (AppDbContext db = new AppDbContext())
{
   string command = "UPDATE Table1 SET Archived=1 
                     WHERE DateCreated < DATEADD(MONTH, -12, GETUTCDATE())";
   db.Database.ExecuteSqlCommand(command);
}*
```

*正如我在开始时提到的，上面的命令是一个原始查询，因此很容易出错。如果数据库模式发生变化，例如，如果您重命名 ***Table1*** 或重命名任意列 ***Archived*** 或 ***DateCreated*** ，上述代码将在运行时中断。但是，您的代码仍然可以很好地编译(查询只是一个文本字符串)。*

*为了避免这样的失败，在应用程序中用惟一的标识符标记这样的代码块是一个好主意，比如添加一个注释`//RAW_QUERY`。使用这样的注释，您可以在每次更新 DB 模式并在查询中进行必要的更改时，在整个应用程序中查找并检查所有这样的情况。*

# *避免在模型类上定义外键*

*根据 EF 文档，建议在数据库级别维护您的外键。如果您使用代码优先迁移，那么您可能需要在模型类上定义外键关系，以便迁移可以自动为您创建它们。我还是建议不要这样。如果数据库模式相对较小，比如少于 20–30 个表，建议在数据库中手动创建 FK，并将模型仅作为表列及其数据类型的表示。*

*在模型类中定义 fk 本身会带来很多复杂性(比如延迟加载)，这超出了本文的范围。即使您这样做了，实体框架也无法确保您的数据不违反任何外键。*

*简而言之，让数据库通过维护关系、键、索引等来维护数据完整性。尽可能只对 CRUD 操作使用实体框架。*

# *给你的桌子命名*

*实体框架遵循多元化模式，从模型类名派生数据库表名。例如，名为`Customer`的模型类将被解释为`Customers`表。这并非不可预测，但对我个人来说感觉模糊不清。例如，一个表名`CustomerInfo`不会变成`CustomerInfos`，但会变成`CustomerInfoes`。另一个问题是你的 DbSet 名称可以完全不同，EF 仍然基于模型类名查询数据库。考虑这个例子:*

```
*//Model Class
public class **Customer** 
{
   public int Id {get; set;}
   public string Name {get; set;}
}//DbSet
public virtual DbSet<**Customer**> **Clients** {get; set;}*
```

*您可以看到，在上面的示例中，模型名称(客户)、数据库集名称(客户)和实际数据库表名称(客户)可以是 3 个不同的值。您可能会问，为什么会在数据库集中出现像“Clients”这样的名称。它发生了，当你在一个项目上工作了几个月或几年，并且发生了许多变化，这很可能会发生。相信我，我是凭经验说的。*

*现在，为了解决这个问题。*

*您可以指示 DbContext 根本不使用复数。但是在这种情况下，您的模型类名应该与数据库表名相同。这似乎不是正确的方法。*

*对我来说，最好的方法是在模型类的顶部声明表名:*

```
*[Table("Customers")]
public class Customer 
{
   public int Id {get; set;}
   public string Name {get; set;}
}*
```

*这样，当您启动一个实体框架命令时，您可以 100%确定在数据库中查询的是什么表名。*

*这些是我在过去多年开发的基础上，对实体框架的最高认识和最佳实践。*

*请在评论中告诉我你的不同意见、改进或想法。*

> *软件永远不会完成，只会被抛弃。*

*为了阅读我所有的故事，以及媒体上成千上万的其他作家，考虑选择媒体会员。如果你[使用我的链接](https://prashantio.medium.com/membership)加入，我将从你的加入费中获得佣金。祝你好运！*