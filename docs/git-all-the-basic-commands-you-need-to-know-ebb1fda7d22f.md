# Git —您需要知道的所有基本命令

> 原文：<https://medium.com/codex/git-all-the-basic-commands-you-need-to-know-ebb1fda7d22f?source=collection_archive---------9----------------------->

Git，它因管理项目文件和跟踪您以及开发人员、程序员和编码设计人员之间的合作者所做的更改而臭名昭著。

![](img/63599b146af079eea338364f0b98e682.png)

图片来源:作者

Git 提供的最大优势是复制项目的能力，在 Git 中也称为“分支”,然后在确定编辑后将更改保存到主文件中。我很高兴我现在在船上，因为它让我免于后悔，也让我不用在我复制的所有文件中找出正确的文件。

我坐下来参加了一个初学者友好的 Git 课程，并试图总结我在这里学到的命令。在这篇文章的最后有一个小惊喜给你。

开始了。

```
git config --global user.name YourName
```

↪用户使用该命令来注册自己，以便对开发人员所做的提交(解释)进行说明。

```
git config --global user.email YourEmail
```

↪用户使用该命令进行提交，并将其注册到用户的电子邮件下。

```
​​git config --global user.name
```

↪该命令允许您重新检查正在进行更改的名称。

```
git config --global user.email
```

↪用户使用此命令来重新检查正在进行更改的电子邮件。

```
git init
```

↪用户使用这个命令来初始化一个空的 git 库。

```
git status
```

↪该命令用于显示存储库中文件的状态。它帮助用户跟踪回购中“未跟踪”文件的数量。

```
git add Yourfile.html
```

↪该命令允许您将特定文件添加/移动到登台区。

```
git add -A
```

↪此命令允许您将所有未跟踪的文件添加/移动到暂存区域。

```
git commit
```

↪此命令提交暂存文件。提交只是意味着保存更改或拍摄文件的快照，以便进行比较或用于恢复。

```
git commit -m “Yourmessage”
```

↪该命令用于提交，但也用于避免 vim 编辑器。

```
git commit -a -m “Yourmessage”
```

↪该命令用于将项目添加到临时区域并同时提交。

```
touch Yourfile.html
```

↪这个命令创建一个文件。

```
git checkout Yourfile.html
```

↪该命令帮助您检索对特定文件所做的最后一次提交。

```
git checkout -f
```

↪该命令帮助您检索所有特定文件的最后一次提交。当需要恢复的文件数量非常大时，这非常有用。

```
git log
```

↪这个命令显示了提交时间和提交者的日志。

```
git log -p -”no. of last commits you want to see the logs of. For eg: 2"
```

↪该命令显示最后一次“请求数量”提交的日志。

```
git diff
```

↪这个命令让你看到已提交版本和未跟踪或未提交版本之间的变化或差异。

```
git diff --staged
```

↪此命令允许您查看已提交/未修改版本和暂存版本之间的更改或差异。

```
git rm --cached Yourfile.html
```

↪该命令允许您从临时区域中删除特定文件。

```
git rm Yourfile.html
```

↪这个命令从硬盘上删除上述文件。

## 。gitignore

。当用户想要忽略文件夹和目录中的特定文件或特定类型的文件时，使用 gitignore。

# Git 分支

分支是 git 最惊人的特性。它允许用户创建一个项目的分支/副本，这样任何要进行和测试的更改都可以在不包含主项目的情况下完成。对分支项目所做的任何更改都不会影响主项目。

这还不是最好的部分，一旦成功地进行了更改和测试，您就可以将分支项目与主项目合并，并像以前一样继续项目。

```
git branch branchname
```

↪该命令允许您使用特定的分支名称创建一个新分支。

```
git branch
```

↪这个命令可以让你看到哪个分支被选中。它会被标上绿色

```
git checkout branchname
```

↪这个命令可以让你在分支之间切换。

```
git merge Yourbranchname
```

↪该命令用于将分支项目与主项目合并。

注意:在执行此命令之前，必须签出到主分支。

```
git checkout -b branchname
```

↪该命令允许您使用该特定分支名称创建一个新分支，并同时切换到该分支。

```
git remote add origin “Repository’s URL”
```

↪这个命令允许你添加你的本地文件/托管你的文件到 Github 来远程访问。

注意:存储库 URL 也可以通过名称“origin”来引用。

“origin”基本上是给你的 GitHub 库的 URL 起的一个名字，这样你就不用每次发出拉/推/取请求时都提到这个 URL。

```
git remote
```

↪这个命令显示你已经添加的库的名称。对我来说，它显示了起源。

```
git remote -v
```

↪这个命令显示了这个库的“获取”和“推送”URL。

```
 git push origin master
```

↪这个命令将主分支中的库推/上传到原点。

```
git push
```

↪该命令将您的存储库推到最后一个默认分支。

```
git push -u origin master
```

↪This 命令允许你改变默认的分支，这个分支应该被推送到 GitHub。

注意:希望你明白上面三个命令的区别，第一个基本上是你，告诉/提到要推哪个分支。其次，它会自动推送到默认分支(由第三个命令设置)。第三，让我们更改默认分支。

```
git remote set-url origin “repository’s new url”
```

↪这个命令改变了“起源”名称下的网址。您可以通过键入以下命令来检查这一更改。

```
git clone “Repository’s URL you wish to clone” <folder name>
```

↪该命令克隆您想要处理的存储库，并将其保存在您指定的文件夹中。

```
git clone “Repository’s URL you wish to clone”
```

↪这也会克隆您想要处理的存储库，但是会将其保存在与存储库同名的文件夹中。

好吧，就这样。如果你觉得这很有帮助，请鼓掌，并关注更多关于技术和编程的精彩内容。

![](img/4cd15b805571d610fa63fdcf6870774a.png)

图片来源:作者

哦，还记得我一开始说的礼物吗。好吧，你不必给这一页加书签。相反，你可以得到我出版的这本电子书。你可以免费得到它，也可以支付任何你想要的东西，让我感到感激。

[](https://bhaveshrawat.gumroad.com/l/lets-git-it-beginners-guide-to-git-bash-commands) [## 让我们得到它！-初学者需要知道的每个命令！

### 我参加了一个初学者友好的 Git 课程，并试图总结我在这本电子书中学到的命令。

bhaveshrawat.gumroad.com](https://bhaveshrawat.gumroad.com/l/lets-git-it-beginners-guide-to-git-bash-commands) 

干杯！