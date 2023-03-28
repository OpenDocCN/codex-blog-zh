# 停止使用 RecyclerView。适配器

> 原文：<https://medium.com/codex/stop-using-recyclerview-adapter-27c77666512b?source=collection_archive---------0----------------------->

## 通过 3 个简单的步骤迁移到您的 RecyclerView 的 ListAdapter。

![](img/4a4b953e187ebaaee7b704e15c042047.png)

格伦·卡斯滕斯-彼得斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

将你的`RecyclerView.Adapter`类迁移到`ListAdapter`只有 3 个简单的步骤。

# 为什么以及何时应该迁移？

当您有一个需要随时间不断变化的回收器视图时，您需要使用 ListAdapter。默认情况下，更改列表项的 RecyclerView 实现是低效的。作为开发人员，我们需要寻找高效的解决方案。

## 什么是困难？

Google 提出了一个名为`DiffUtil`的类，它将当前已有的列表与新提交的列表进行比较；它只在屏幕上改变必要的用户界面。然而，这增加了已经很复杂的样板代码，因此，我们可以使用`ListAdapter`来减少样板代码。

# #0 —开始使用

这是您在这里看到的传统 RecyclerView 适配器。我们将修改这个类，改为使用`ListAdapter`。

# #1 —创建 DiffUtil 回调

如前所述，我们需要使用 DiffUtil 来有效地比较列表。

我们可以从创建接口的静态匿名对象开始。这里我们有两个功能:

1.  `areItemsTheSame` —如果我们比较的项目相同，此函数返回 true。我们通常通过比较模态的主键来做到这一点。
2.  `areContentsTheSame` —如果项目的其他属性相同，此函数返回 true。这将检查项目本身是否有任何内部更改。

# #2 —替换继承的类

现在我们可以将`RecyclerView.Adapter`替换为`ListAdapter`。

我们在父构造函数中传递`DiffUtil`的对象。我们已经成功地将我们的类迁移到了一个`ListAdapter`；然而，我们的代码仍然不是我们所需要的。

# #3 —添加最后的润色。

既然您正在使用`ListAdapter`，那么利用我们从中获得的能力是有意义的。

1.  您可以移除`getItemCount`功能。`ListAdapter`为您实现。
2.  如下图所示替换`list`属性。
3.  将`list[position]`改为`getItem(position)`。

# 结论

通过一些小的改动，并遵循这三个简单的步骤，您可以显著提高应用程序的性能。`ListAdapter`固有地使用了`RecyclerView.Adapter`并使其性能更好。如果你的列表不断变化，那么使用`ListAdapter`总是一个好的选择。

我希望你喜欢读我的文章，并学到了一些东西。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```