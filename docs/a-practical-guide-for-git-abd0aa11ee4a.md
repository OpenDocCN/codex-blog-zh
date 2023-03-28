# Git 实用指南

> 原文：<https://medium.com/codex/a-practical-guide-for-git-abd0aa11ee4a?source=collection_archive---------5----------------------->

这是 git 的实用指南，主要面向那些不知道什么和如何使用 git 的人。如果你知道有人不知道如何使用 git，把这篇文章发给他们。

![](img/c966d9e34de4443f04a30b4d52652ec9.png)

[扬西·敏](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# Git 是什么

> Git 是一个快速的、可伸缩的、分布式的版本控制系统，具有异常丰富的命令集，提供了高级操作和对内部的完全访问。

这意味着它是一个版本控制系统，跟踪对一个/多个文件所做的更改。

# 如何安装 Git

**Windows**
为 Windows 安装 git[链接](https://gitforwindows.org/)
**Mac**

```
brew install git
```

**Ubuntu**

```
sudo apt-get install git-all
```

**拱门**

```
sudo pacman -S git
```

# 如何配置 Git

当你第一次安装 git 时，你需要添加你的用户名和电子邮件。
添加您的用户名

```
git config --global user.name "username"
```

添加您的电子邮件

```
git config --global user.email "email"
```

# Git 的基本命令

## **1。git 添加**

git add 命令用于向 git 添加文件，以便 git 可以跟踪。当您通过 git add 命令添加文件时，它们会被暂存。你可以把它看作是一件事情开始和结束的中间阶段。语法:-

```
# For adding one file
git add (filename)
# Adding multiple files
git add (filename1) (filename2)
# For adding all the files in a folder
git add *
```

## **2。git 提交**

现在你已经用`git add`暂存并添加了你的文件，为了完成它，你需要运行 git commit 命令。它后面必须跟一个`-m`标签，并且你必须用引号键入消息，声明你的提交(你提交的是什么),你也可以键入垃圾，但是这违背了`-m`标签的目的。

```
# Commiting a file 
git commit -m "Commit Message"
# Example 2
git commit -m "Refactored the code"
```

## **3。Git pull**

git pull 命令下载存储库中存在的所有文件。建议在推送之前进行 git 拉取，因为这样可以减少合并冲突。想象你和你的朋友正在做一个项目(创建一个网站)。你正在开发 CSS，而你的朋友正在开发 HTML。当您完成了您的部分并计划将代码推送到存储库时，您的朋友已经对文件进行了一些更改。但是这些更改在您的系统中并不存在，因此为了下载您的朋友所做的更改，您需要执行 git pull。

```
# Git pull command
git pull
```

## **4。Git 推送**

git push 命令将更改和新文件发送到存储库。现在，如果你问什么是存储库，就把它想象成一个在线文件夹，你所有的同事都在那里贡献他们的工作。

```
# Git push command
git push
```

## **5。Git 状态**

git status 命令显示您跟踪的、未跟踪的、与工作树不同的文件。

```
# git status command
git status
```

当您进行 git 推送时，git 会要求您提供凭证。有几种方法可以验证它，我的首选方法是使用 SSH 密钥。我将在此基础上发布一篇新文章，请关注我。

> 如果你喜欢这个博客，请别忘了鼓掌。如果你想了解更多关于隐私、安全、技术和 Linux 的知识，可以考虑关注我。另外，任何我觉得有趣和有价值的东西。