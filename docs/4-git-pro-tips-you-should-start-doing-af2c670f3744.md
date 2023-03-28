# 你应该开始做的 4 个 Git Pro 提示

> 原文：<https://medium.com/codex/4-git-pro-tips-you-should-start-doing-af2c670f3744?source=collection_archive---------6----------------------->

## 你和你的同事会更爱你。

![](img/4b9bb562beb03b1d4defd147579fd191.png)

[李奇伟](https://unsplash.com/@richygreat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果你在这里，你知道你在做什么，所以让我们开门见山。

# 永远不要将损坏的代码推送到 main 或 master。总是使用一个分支来调整和测试你的代码。

你知道克隆一个回购协议第一次尝试就失败有多烦人吗？这是一个令人沮丧的事情。一些人坚持推进到主分支或主要分支，即使他们没有正确地测试代码调整。不要成为那些人。

如果需要更改代码，可以从主节点创建一个分支。例如，将您的分支命名为`john_tweak`。

```
git branch john_tweak
```

不要忘记检查您新创建的分支。

```
git checkout john_tweak
```

现在，您可以随意修改/添加/提交您的代码。

一旦代码完成并正确测试，你就可以将`john_tweak` 与`master`分支合并。如果编辑主分支，您将会遇到一些冲突。解决它们，然后就可以推了。

```
git checkout master
git merge john_tweak
git push origin master
```

# 总是给主要提交添加一个标记。

标签是救命恩人。我遇到过无数次需要一个特定版本的存储库的情况，无论是恢复到那个版本，还是因为当前的主分支混乱而临时使用它。

有了标记，你就可以得到一个人性化的指示器，显示在特定时间的进度。您希望向当前的完美提交添加一个标记。有时客户希望看到进度，如果没有标签，您可能不得不显示当前开发中的损坏的特性。永远记住，没有什么特性比破损的特性更重要。

很容易标记。

```
git tag stable/1.0.0
```

不要忘记将标签也推送到存储库。

```
git push origin master --tags
```

在 git 混乱情况下，您可以检查稳定的提交。

```
git checkout stable/1.0.0
```

你也可以通过标记来帮助你的朋友。与其争论哪个提交 ID，不如用标签来争论。

# 始终添加一个自述文件。求你了。

没有说明的源代码很气人。我该拿你的代码怎么办？一行一行地读？

看到人们没有适当的文件是可笑的。看到有人不告诉别人如何使用自己的代码，那就更可笑了。他们希望有人伸出援手。他们想感觉自己很聪明。

帮大家一个忙。至少写一个自述文件，关于如何运行代码，依赖关系，需求等。最多只需要 30 分钟。

# 推动时使用 set-upstream。

打字不累吗:

```
git push origin master
```

幸运的是，你不需要。您可以将特定的远程和分支设置为默认的推送储存库。例如，如果您希望您的原点/主点成为您的默认值，请执行以下操作:

```
git push --set-upstream origin master
```

下次你需要用力时，你只需要:

```
git push
```

干净，快速，简单。

# 外卖食品

通过实施这 4 个行为，你的 git 生活将会更加顺畅。相信我。