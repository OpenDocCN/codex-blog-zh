# 清除提交而不重设基础

> 原文：<https://medium.com/codex/clean-commits-without-rebasing-d409ec18953b?source=collection_archive---------12----------------------->

# 如何避免重置基址并保持干净的提交历史。

避免在一个拉请求上有太多的提交是一个好习惯。然而，为了避免丢失您的更改，以防您的计算机崩溃，另一个好的做法是经常提交并将您的提交推送到云。

![](img/63714040d3d253fb79844c54db9c503b.png)

[https://pinterest.com/pin/664562488755915683/](https://br.pinterest.com/pin/664562488755915683/)

此外，在处理票据的过程中，您可能必须每隔一段时间与基础分支合并，以获得最新的更改并避免冲突。

![](img/50084d110cf732e66cd824d665119cc9.png)

假设您已经完成了您的工作，并且您想要与主分支合并，并且在这个过程中，您还想要清除您在处理特性时推送到远程的任何提交。最后，您只希望对您的拉请求进行一次提交(也许是几次提交)。经典的做法是将 **git merge** 和 **git rebase 结合起来。**

我想给你一个替代方案，希望能让这个过程不那么痛苦。

假设您已经检查了您的分支:

```
$ **git pull origin main**
```

**用*main*更新你的分支。Main 是你刚开始工作时的基础分支(如果是这样的话就改成 **master** 或者 **develop** )。在这一点上，您有了所有以前的提交+一个合并提交，以及来自基础分支的最新更改(在这之后修复任何可能的冲突)。**

```
$ **git reset origin/main --soft**
```

**将您的更改放在基础分支之上。**这将重置所有提交，并保存您所做的更改，为最终提交做好准备。在这之后，您可能需要添加任何未分级的更改(git add -A)。

```
$ **git commit -m "my beautiful commit message"**
```

**承诺一切。**这就像你所有的修改都是在一次提交中完成的。

```
$ **git push origin [my-branch] --force-with-lease**
```

**将您的更改推送到远程分支。**需要 *— force-with-lease* 标志，因为您正在重写分支上的提交历史，用新的提交替换任何提交。

谁说不能改写历史了？

![](img/7c25a937b0c09b2a3cde01678af3859d.png)

# 一个命令来统治他们所有人

这基本上是上面一行中的所有命令

```
$ **git pull origin [base-branch] && git reset origin/[base-branch] && git commit -m "my message" git push origin my-branch**
```

# 奖金(1)

让我们假设，在你完成了上述所有步骤后，你在你的公关上得到一条评论，要求改变某些东西，而你不想再重新设置它。

更改您需要的内容，并将它们全部添加到 **git add -A.** 中提交

现在你有两个选择，**第一个选择:**

```
$ **git commit --amend --no-edit**
```

*   *- amend* 标志用于更新最后一次提交的更多更改。
*   *- no-edit* 标志表示您不想更改提交消息。

**第二选项:**

```
$ **git commit --amend -m "new commit message"**
```

*   这用更多的变化修正了最后一次提交，并更新了先前的提交消息。

在上面的一个选项之后，您只需要用 force 标志再次推送所有内容来覆盖远程提交(**git push origin[my-branch]-f**)。

# 奖金(2)

强制力与租赁强制力的区别

> 用本地分支覆盖远程分支。
> 
> `*--force-with-lease*`是一个更安全的选项，如果更多的提交被添加到远程分支，它不会覆盖远程分支上的任何工作。它确保你不会通过强行推动来覆盖别人的工作。

更多详情请点击此处:

[](https://stackoverflow.com/a/52823955/3765819) [## git push-force-with-lease 与-force

### 从可信和/或官方来源寻找答案。托雷克在《》中提到的“比较和交换”

stackoverflow.com](https://stackoverflow.com/a/52823955/3765819) 

如果你想有干净的提交，希望这能让你的生活更容易。感谢阅读！