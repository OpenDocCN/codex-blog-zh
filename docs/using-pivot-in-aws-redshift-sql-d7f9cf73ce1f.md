# 在 AWS 红移 SQL 中使用 Pivot

> 原文：<https://medium.com/codex/using-pivot-in-aws-redshift-sql-d7f9cf73ce1f?source=collection_archive---------6----------------------->

## 如何使用透视功能

![](img/67a83bb05bc99f64b93c4ad182d5faf3.png)

由[戴夫·霍夫勒](https://unsplash.com/@davehoefler?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/washington-state?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

如果你像我一样经常使用 Amazon Redshift，了解一些重要的基本函数会非常有用，这会让你的工作变得更加容易。

其中一个是`PIVOT`和`UNPIVOT`函数，这使得(取消)透视表相对容易。数据透视表是一种特殊类型的表格…