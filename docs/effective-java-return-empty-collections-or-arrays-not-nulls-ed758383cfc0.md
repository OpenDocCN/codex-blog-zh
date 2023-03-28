# 有效的 Java！返回空集合或数组，而不是空值

> 原文：<https://medium.com/codex/effective-java-return-empty-collections-or-arrays-not-nulls-ed758383cfc0?source=collection_archive---------3----------------------->

![](img/1d9b5e0b08d190c628d7a95d667dc5e2.png)

汉斯·艾斯科宁在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

类似如下的代码并不少见:

```
private final List<Chesse> cheesesInStock = ...public List<Cheese> getInStockCheeses() {
  return cheesesInStock.isEmpty() ? null : new ArrayList<>(cheesesInStock);
}
```

方法的使用看起来像这样:

```
List<Cheese> cheeses…
```