# 创建可应用于不同类型的可重用 Linq 过滤器(Where 的谓词生成器)

> 原文：<https://medium.com/codex/creating-reusable-linq-filters-predicate-builders-for-where-that-can-be-applied-to-different-f7b292c6c9a7?source=collection_archive---------3----------------------->

一种创建可重用 Linq 筛选器(Where 子句的谓词生成器)的方法，这些筛选器可应用于不同类型的对象。要筛选的对象字段是使用 MemberExpression 指定的。

该方法适用于实体框架，包括异步操作。

# 主旨。什么是可重复使用的过滤器？

例如，有订单:

```
class Order {
   public DateTime Start { get; set; }
   public DateTime? End { get; set; }
}
```

您需要找到未来 7 天内有效的所有订单。

使用可重复使用的过滤器构建器(如果已实现)，您可以找到如下订单:

```
var ordersFiltred = orders
   .WhereOverlap(
      *//* using MemberExpressions      *//* set up which fields to search for      fromField: oo => oo.Start,
      toField: oo => oo.End, *//* set up the search period      from: DateTime.Now,
      to: DateTime.Now.AddDays(7))
   .ToList();
```

相同的 WhereOverlap 可以重用并应用于另一个类型。例如，要搜索商务旅行:

```
class Trip {
   public DateTime? From { get; set; }
   public DateTime? To { get; set; }
}var tripsFiltred = trips
   .WhereOverlap(
      *//* using MemberExpressions      *//* set up which fields to search for      fromField: oo => oo.From,
      toField: oo => oo.To, *//* set up the search period
      from: DateTime.Now,
      to: DateTime.Now.AddDays(7))
   .ToList();
```

订单和商务旅行是不同类型的对象，它们没有通用的界面，并且搜索字段的名称也不同。然而，对于类型(订单和商务旅行)，有一个可重用的 WhereOverlap 过滤器。

下面描述了如何创建这样的可重用谓词生成器。

# 如何制作可重复使用的过滤器

上面描述了 WhereOverlap 的使用，显示其实现是合乎逻辑的。但是为了使 WhereOverlap，您需要实现操作符“AND”、“OR”。所以让我们从一个更简单的例子开始。

例如，有支出和溢价:

```
class Payout {
   public decimal Total { get; set; }
   public bool UnderControl { get; set; }
}class Premium {
   public decimal Sum { get; set; }
   public bool RequiresConfirmation { get; set; }
}
```

让我们制作一个可重复使用的过滤器来查找超过一定金额的付款:

```
class UnderControlPayFilter {
   readonly decimal Limit;
   public UnderControlPayFilter(decimal limit) {
      Limit = limit;
   } public Expression<Func<TEnt, bool>> Create<TEnt>(
      Expression<Func<TEnt, decimal>> sumField) { /*/ GreaterOrEqual -* need to be implemented      *// GreaterOrEqual - is extension,* which takes arguments   *//  - field* designation*(Expression sumField)
*      *//  - and* the value to be compared with *(Limit)
   *   return sumField.GreaterOrEqual(Limit);
   }
}
```

使用 UnderControlPayFilter 过滤器的示例:

```
*//* search filter for payments requiring additional control
*//
//* the specific limit (here 1000) can be taken out in the settings*,
//* and the UnderControlPayFilter can be registered
// in the IoC container*.
//* Then you can centrally (through the application settings) *//* control the maximum limitvar underControlPayFilter = new UnderControlPayFilter(1000);//
// Applying a reusable filter for payoutsvar payoutPredicate =
   underControlPayFilter.Create<Payout>(pp => pp.Total);// here, for simplicity, payouts is an array,
// in a real application this can be an Entity Framework DbSet
var payouts = new[] {
   new Payout{ Total = 100 },
   new Payout{ Total = 50, UnderControl = true },
   new Payout{ Total = 25.5m },
   new Payout{ Total = 1050.67m }
}
.AsQueryable()
.Where(payoutPredicate)
.ToList(); //
// Applying a reusable filter for premiumsvar premiumPredicate =
   underControlPayFilter.Create<Premium>(pp => pp.Sum);// here, for simplicity, payouts is an array,
// in a real application this can be an Entity Framework DbSet
var premiums = new[] {
   new Premium{ Sum = 2000 },
   new Premium{ Sum = 50.08m },
   new Premium{ Sum = 25.5m, RequiresConfirmation = true },
   new Premium{ Sum = 1070.07m } }
.AsQueryable()
.Where(premiumPredicate)
.ToList();
```

万事俱备，只欠实现更大的扩展:

```
public static class MemberExpressionExtensions {
   public static Expression<Func<TEnt, bool>>
   GreaterOrEqual<TEnt, TProp>(
      this Expression<Func<TEnt, TProp>> field, TProp val) => Expression.Lambda<Func<TEnt, bool>>(
            Expression.GreaterThanOrEqual(
               field.Body, Expression.Constant(val, typeof(TProp))),
            field.Parameters); }
```

同样，您可以实现 LessOrEqual、Equal、HasNoVal 等扩展。

# 使用 AND 和 OR 运算符的更复杂的可重用过滤器

例如，您需要查找超过一定金额的付款以及那些特别标记为需要额外监控的付款。

让我们修改下面的 ControlPayFilter:

```
class UnderControlPayFilter {
   readonly decimal Limit;
   public UnderControlPayFilter(decimal limit) {
      Limit = limit;  
   } public Expression<Func<TEnt, bool>> Create<TEnt>(
      Expression<Func<TEnt, decimal>> sumField,
      Expression<Func<TEnt, bool>> controlMarkField) { *// PredicateBuilder* need to be implemented         return PredicateBuilder.Or(
            sumField.GreaterOrEqual(Limit),
            controlMarkField.Equal(true));
   }
}
```

使用示例:

```
*// for payouts* var payoutPredicate =
   underControlPayFilter.Create<Payout>(
      sumField: pp => pp.Total,
      controlMarkField: pp => pp.UnderControl);*// for premiums* var premiumPredicate =
   underControlPayFilter.Create<Premium>(
      sumField: pp => pp.Sum,
      controlMarkField: pp => pp.RequiresConfirmation);
```

PredicateBuilder 是由 Pete Montgomery 开发的一个通用的 PredicateBuilder。

# 结论

要制作自己的可重用过滤器，你只需要 [PredicateBuilder](https://petemontgomery.wordpress.com/2011/02/10/a-universal-predicatebuilder/) 和[MemberExpressionExtensions](https://github.com/AlexeyBoiko/LinqExpr/blob/main/LinqExpr/MemberExpressionExtensions.cs)。只需将它们复制到您的项目中。可重用的过滤器可以被设计为扩展(如 WhereOverlap)、静态帮助器或类(如 UnderControlPayFilter)。

我做了几个可重复使用的过滤器——[NuGet](https://www.nuget.org/packages/LinqExpr/)， [GitHub](https://github.com/AlexeyBoiko/LinqExpr) 。