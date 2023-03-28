# 如何在数组中搜索项目

> 原文：<https://medium.com/codex/how-to-search-for-an-item-in-an-array-392048898c9b?source=collection_archive---------29----------------------->

## 简单的答案是 array.find，但是还有更有效的方法。

![](img/56ccc1e3745f1e0f8ebb1565bab56d62.png)

米克·豪普特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

> 在进入不同的查找数组的方法之前，array.find 或 array.indexOf 将是首选的方法，**如果**你正在做一次或两次。

使用 array.find 查找

然而，如果你需要多次查找同一个数组，这就有点费钱了，因为在这个例子中我们对同一个数组迭代了 3 次。

这就是地图的用处。不管你需要搜索一个数组多少次，我们只会遍历数组**一次**。

基本的想法是，你想把一个数组转换成一个映射，它将作为一个散列表工作。因为查找哈希表比查找数组快，所以这会给你的函数带来很大的提升。

使用地图，正如你所看到的，迭代只需要发生一次。之后我们可以快速查找项目。我们还可以使用这个映射，通过 has 方法来检查数组中是否存在某个条目。如果您的功能只需要检查一个项目是否存在，请查看此[文章](https://shresthaprince.medium.com/how-to-check-if-an-item-exists-in-an-array-77d1612ecc63)。

> 通过使用映射，您可以使用更多的内存来获得更好的性能，因此正如在开始时提到的，如果您只是几次查找数组，那么使用 array.find 会更好。您还可以使用 JS 对象(比 map 占用更少的内存)以类似的方式创建一个 hash map。