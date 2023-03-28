# 如何检查数组中是否存在某项

> 原文：<https://medium.com/codex/how-to-check-if-an-item-exists-in-an-array-77d1612ecc63?source=collection_archive---------27----------------------->

## 还有比 array.findIndex 更好的方法吗？

![](img/44453f278ea80ef2489e4d2c4bc70384.png)

马库斯·温克勒在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

假设你有一个数字数组，你想检查数组中是否存在某个数字。您可以使用 indexOf(如果它是一个基本类型的数组)或 findIndex，并检查结果是-1 (false)还是 item 的索引(true)。

```
const numbers = [1, 2, 3, 4];const does1Exist = numbers.indexOf(1) !== -1; => true
const does6Exist = numbers.indexOf(6) !== -1; => falseconst fruits = [
{ name: "apple", quantity: 1 },
{ name: "banana", quantity: 2 },
];const doesAppleExist =
fruits.findIndex((fruit) => fruit.name === "apple") !== -1;doesAppleExist => true
```

现在，假设我们需要检查数组中是否有 100 个水果。我们会逃跑。findIndex 100 次(在同一个数组上运行 100 次迭代)。在这种情况下，我们应该使用集合。

请注意，通过创建一个集合，您将使用更多的内存，因此，如果您只是几次查找数组，这种方法就不合适了。

使用集合，我们只能知道该项目是否存在于数组中。如果我们需要查找的项目呢？为此，我们需要使用地图或物体，你可以查看这篇[文章](https://shresthaprince.medium.com/how-to-search-for-an-item-in-an-array-392048898c9b)。