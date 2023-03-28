# 当 DRY 成为反模式:代码中的代码

> 原文：<https://medium.com/codex/when-dry-becomes-an-anti-pattern-code-in-code-87b1caab791f?source=collection_archive---------3----------------------->

不幸的是，拥有可读代码没有普遍的原则。我经常在想，哪一套原则应该放在第一位:YAGNI？干？固体？。我相信最好的原则还没有被编纂。所以我想建立一个新的，可读性是最重要的(RIMI)。

您可以找到许多专家讨论代码可读性的重要性。这里有一个让你开始的参考:[https://blog.pragmaticengineer.com/readable-code/](https://blog.pragmaticengineer.com/readable-code/)

根据 RIMI 的主要原则，我们将探索几个明显不适用于 DRY 的场景。本文探讨第一种场景。

![](img/a1c7d7ea12302f38dc62ff4c96ec82ee.png)

像沙漠一样干燥

# 配置的相同结构功能

这是函数的行为根据参数而变化的地方。这可能是最不适合干燥的地方。

考虑以下 ETL 数据场景:

```
void ImportData(sourceQuery, tableRoot, transformQuery, loadQuery)
{
    var sourceData = ReadFromSource(sourceQuery);
    var stagingTableName = tableRoot + "_Staging";
    WriteToStaging(sourceData, stagingTableName);
    TransformData(transformQuery);
    LoadData(loadQuery);
}

void Main()
{
    ImportData("SELECT *...", "Cars", "UPDATE Cars_Staging...", "UPSERT Cars...");
    ImportData("SELECT *...", "Mechanics", "UPDATE Mechanics_Staging...", "UPSERT Mechanics...");
}
```

SQL 参数不在上下文中，您必须在 ImportData 中查看它们的用途。尽管完成这样的事情感觉很“聪明”，但阅读起来很困难。

它也很难修改。如果需要处理一个新表，但是需要 2x 转换步骤，那么 ImportData 函数将变得更加复杂，并且需要处理可选的空参数。这种越来越复杂的情况实际上比我的经验要糟糕得多。程序员从简单的需求中创建这种不可读的模式，但是随着时间的推移，需求总是变得更加复杂。

改进后的设计如下:

```
void Main()
{
    ImportCars();
    ImportMechanics();
}

void ImportCars()
{
    var sourceData = ReadFromSource("SELECT *...");
    WriteToStaging(sourceData, "Cars_Staging");
    TransformData("UPDATE Cars_Staging...");
    LoadData("UPSERT Mechanics...");
}

void ImportMechanics(sourceQuery, tableRoot, transformQuery, loadQuery)
{
    var sourceData = ReadFromSource("SELECT *...");
    WriteToStaging(sourceData, Mechanics_Staging);
    TransformData("UPDATE Mechanics_Staging...");
    TransformData("UPDATE Mechanics_Staging..."); //Second transformation
    LoadData("UPSERT Mechanics...");
}
```

我在 ImportMechanics 函数中包含了第二个转换。看到与“结构”的背离仅仅需要一行额外的代码了吗？这很好地表明了之前有问题的代码模式是“代码中的代码”反模式。

所以这显示了对 DRY 原则的意识形态追求是如何导致糟糕的代码设计的。

避免这种“代码中的代码”情况的最好方法是尝试和想象几种设计，并选择最好的。你需要尝试并确定你的想法*是否可能*是代码中代码，以及替代设计是否只需要多一行代码。

如果你担心创建 100 个相同模式的“重复”，你应该搭建你的代码。生成代码意味着您可以模板化正确的模式，使代码非常可读和可维护。

请继续关注我，我将探索更多的情况，在这些情况下，干巴巴或什么都不做的态度会导致反模式。更新:参见[当 Dry 成为反模式:抽象层](/codex/when-dry-becomes-an-anti-pattern-abstraction-layers-540b9f0cb831)

更新:我后来发现了 AHA，就是“避免草率的抽象”。你可以在维基百科上读到关于干燥的文章:[https://en.wikipedia.org/wiki/Don%27t_repeat_yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)