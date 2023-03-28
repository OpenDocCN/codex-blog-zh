# Git 中的基本操作

> 原文：<https://medium.com/codex/basic-operations-in-git-72714ded4180?source=collection_archive---------4----------------------->

## [法典](http://medium.com/codex)

![](img/f388756d87c38bd70069a5c2052dcedb.png)

由[卢克·切瑟](https://unsplash.com/@lukechesser?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我之前的文章中，我已经介绍过[Git 和 Github 的介绍](https://sameerkatija.medium.com/an-introduction-to-git-and-github-for-newbies-496b2f51df71)和[如何将项目推送到 GitHub](https://sameerkatija.medium.com/how-to-push-a-project-to-github-d099523eb5b7) 。今天我们将学习 git 的基本操作。Git 中几乎有五种基本操作。

1.  正在初始化
2.  增加
3.  犯罪
4.  拉
5.  推

如果您已经阅读了我以前的文章，您可能会对这些操作有所了解。让我们来讨论一下它们，但是在此之前，如果你不知道 Git，请先阅读我的文章“[Git 和 Github](https://sameerkatija.medium.com/an-introduction-to-git-and-github-for-newbies-496b2f51df71) 简介”，然后继续阅读本文。如果你有 git 的诀窍。我们继续。

## 正在初始化

Git 中的初始化用于将简单的文件夹转换为 Git 存储库。要将文件夹初始化为 git repo，您需要浏览要初始化为 Git 的文件夹，启动 Git 终端并运行下面给出的命令。

```
git init
```

现在，您的文件夹被初始化为一个 Git repo，它将有一个名为。git”，它可能不可见，但如果您将设置更改为“显示隐藏的文件夹”，您可以看到。初始化的 repo 也称为“工作目录”。

*项目的根文件夹通常被称为“工作副本”或“工作主管”。它是本地计算机上包含项目文件的目录。*

## 增加

添加操作用于将工作目录中的变更添加到暂存区。*暂存区是一个虚拟的地方，它收集了您想要在下一次提交中包含的所有文件。*

它的 Git add 操作告诉 Git 您希望在下一次提交中包含特定文件的更新。然而，添加操作并不会真正以任何方式影响存储库，并且在提交代码之前，不会实际记录更改。添加操作是使用

```
git add <filename> (for specific file)
git add -a (for all files)
```

## 犯罪

git 中的提交操作用于保存暂存区中的所有更改，以及本地存储库中用户的简短描述。提交在 Git 中非常重要。您可以将每次提交视为项目的快照，其中在当前存储库中创建了项目的新版本。Git 提交是使用

```
git commit -m “user message”
```

**拉**

pull 操作用于从远程存储库中获取和下载数据，并将其合并到本地存储库中，以匹配该代码或数据。Git 拉取是使用

```
git pull
```

## 推

推送操作用于将本地存储库数据上传到远程存储库。Git 推送是使用

```
git push origin main
```

现在，您已经了解了 git 的基本操作。在下一篇文章中，我们将关注高级 Git 操作。如果你发现有帮助，不要忘记分享。