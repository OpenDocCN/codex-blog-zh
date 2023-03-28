# 处理大型数据文件和糟糕的 Git 提交

> 原文：<https://medium.com/codex/dealing-with-large-data-files-and-bad-git-commits-67e01d92fb03?source=collection_archive---------4----------------------->

## 又名 Git 壁球

![](img/624470aa3eef552c553eb0cebf5d873d.png)

弗洛里安·奥利佛在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

随着我更深入地寻找工作，我开始了一个新的数据科学项目，以拓宽我的技能组合，并更好地与我的职业目标相关联。这个项目的主要目标是利用卫星图像和并行神经网络。更具体地说，我从 Kaggle 检索了数据，并将对云的类型进行分类，以便更好地了解天气模式。

我已经下载了一个数据集，我意识到它比我习惯使用的要大，并且在试图把它推送到 GitHub 时完全卡住了！

我发现了这个问题，并认为其他人可能会在这里找到有帮助的书面解决方案。

*发生了什么:*

在连接到 Kaggle API 并下载了超过 5GB 的数据集之后，我刚刚“git commit”了对我的新 repo 的第一次更改。

Git 推送…

我的终端让我想到了以下内容:

```
remote: **error**: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.github.com.remote: **error**: See http://git.io/iEPt8g for more information.To https://github.com/oac0de/Understanding_Cloud_Formations! [remote rejected] main -> main (pre-receive hook declined)error: failed to push some refs to 'https://github.com/oac0de/Understanding_Cloud_Formations'
```

在这一点上，我记得 GitHub 的标准数据传输限制是一个坚定的 100 MB！我受够了。实际上 Git 已经提交了我的修改，但是 GitHub 网站限制了我的使用，原因很明显，是为了节省服务器空间。

我在这里能做什么？我以为我可以删除文件，重新提交更改，然后推送，对吗？没有。我仍然收到错误消息。

正如我不断学习的那样，当你‘git commit’某事时，你实际上是在 git stone 上蚀刻你的变化，直到永远。不完全是。但是您实际上是将所有的更改和提交保存到您的 git 历史中。这实际上是 git 如此强大的版本控制软件的原因之一。多亏了可信赖的 StackExchange，有了一个变通办法。

在 git 推送问题上贴上创可贴，这样你至少可以上传你的代码，删除你的大文件。

```
git rm --cached [your_large_file_name]
```

接下来，你执行 GitHub 所说的“挤压”,将你最近提交的 git 压缩成一个。这里我们将挤压最后两次提交，因为我已经搞砸了第二次。

```
git reset --soft HEAD~2
```

压扁了！现在只需为组合提交输入一条消息:

```
git commit -m "New message for the combined commit"
git push
```

瞧。我们的代码被推送到我们的远程存储库，只是没有我们的大数据文件。

为了避免将来出现这些问题，最佳做法是将数据集(如果大于 100MB)实际添加到。gitignore 文件。为此，如果您还没有. gitignore 文件，可以从终端创建一个:

```
touch .gitignore
```

然后打开它。gitignore 文件:

```
open .gitignore
```

这将调出您的。gitignore 在一些文本编辑器中取决于你的操作系统。GitHub 上有一个很好的[模板，用于 Python 项目，处理日常事务。只需将所有这些内容复制并粘贴到您打开的。gitignore 文件。](https://github.com/github/gitignore/blob/master/Python.gitignore)

您还需要为大型数据文件添加额外的行。只需包含文件的路径和文件名，就可以开始了！

我试验和研究得越多，就越发现更好地了解 Git 的内部工作方式真的可以节省很多时间。但是，当未知类型的问题出现时，我们真的应该感谢无私的专业编码人员，他们为我们这些 StackExchange 和 StackOverflow 的新手贡献了专业知识。救命恩人！