# N + 1 查询问题(Django 示例)

> 原文：<https://medium.com/codex/n-1-queries-problem-a-django-example-fd57e3a1761e?source=collection_archive---------4----------------------->

## 严重的性能问题。

![](img/f8c18b0e5f1350c4122f4beb1d4c6afd.png)

摘自马库斯·温克勒的《Unsplash》

在这篇文章中，我们将学习 N + 1 查询问题，它是什么以及如何解决它。

# **什么是 N + 1 查询问题？**

简而言之，N + 1 查询问题是一个性能反模式，它发生在针对前一个查询的每个结果执行查询时。这里是你从第一个查询中得到的每个结果的查询次数。这意味着如果我们执行一个查询，得到 200 个结果，那么 N + 1 = 201 个查询。

让我们看一个例子来熟悉这个问题。假设我们有这样的代码:

在这个简单的例子中，我们有 posts QuerySet 和一个 for 循环，该循环遍历它并打印每篇文章的标题和作者。通过查看代码，您会发现我们有一个 Post 模型和一个 Author 模型。这告诉您 author 属性是一个外键。

让我们看看我们的代码执行了多少查询:

首先，我们已经创建了 **Post** QuerySet，所以到目前为止，我们已经执行了 0 个查询。为什么？因为 QuerySets 很懒。我引用 Django 的文件:

> *“查询集是懒惰的——创建* `*QuerySet*` *的行为不涉及任何数据库活动。您可以整天将过滤器堆叠在一起，Django 不会真正运行查询，直到对* `*QuerySet*` *求值。”*

您可以在此阅读更多关于此表格的信息[。](https://docs.djangoproject.com/en/4.1/topics/db/queries/#querysets-are-lazy)

然后，我们对帖子进行迭代，这将导致 QuerySet 评估并获取其结果。现在，我们有一个查询。

之后，我们打印与文章对应的标题和作者。由于标题是文章本身的一个字段，我们将没有额外的查询，因为它已经在第一个查询中被获取。但是作者呢？我们记得它是一个外键，所以 Django 没有获取它。因此，Django 将通过执行另一个查询来获取给定 author_id 的相应名称(显然，author 模型包含一个名称字段)，从而做额外的工作。

到目前为止，我们已经执行了 **1 个**查询来获取文章，执行了 **N 个**查询来获取作者。 **N** 是岗位数量。

想象一下，我们有 100 万个帖子😆。

# 如何解决这个问题？

**select_related()** 和 **prefetch_related()** 是两个 QuerySet 方法，提供了解决 N+1 查询问题的解决方案。

[**select_related()**](https://docs.djangoproject.com/en/4.1/ref/models/querysets/#django.db.models.query.QuerySet.select_related) 在执行查询时，返回一个选择附加相关对象数据的查询集。使用此方法后，我们之前的代码将如下所示:

现在我们只有 **1** 查询来选择文章及其相关作者。

[**prefetch _ related**](https://docs.djangoproject.com/en/4.1/ref/models/querysets/#django.db.models.query.QuerySet.prefetch_related)**()**返回一个 QuerySet，它将在单个批处理中自动检索每个指定查找的相关对象。现在，代码将如下所示:

这两种方法的区别在于 **select_related()** 创建一个 SQL 连接，并在 select 语句中包含相关对象数据。它只适用于外键和一对一的关系。另一方面， **prefetch_related()** 对每个关系进行单独的查找，并使用 Python 执行连接。这使我们能够预取各种关系对象，它们是:*多对多、多对一、外键和一对一关系。*

有一些工具可以让你发现 N + 1 个查询问题，但是我不会在这篇短文中讨论它们。

感谢你阅读这篇文章，❤

在 [Linkedin](https://www.linkedin.com/in/younes-belouche-641bb3197/) 、 [Instagram](https://www.instagram.com/younes_belouche/) 和 [Github](https://github.com/dombroks) 上和我联系。