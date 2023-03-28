# 吉特:这到底是什么？

> 原文：<https://medium.com/codex/git-what-is-it-anyway-7fe34d40ae0e?source=collection_archive---------7----------------------->

## [法典](http://medium.com/codex)

## git 内部是如何工作的，为什么这么棒？

我们喜欢 GIT。我们爱 GIT。因为它每天能拯救我们的生命和时间几十次，让我们快乐:)

![](img/3921bf2c312182828f0320b0ee621543.png)

照片由 [Yancy Min](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我们一直在 **git pull** ing 和 **git push** ing，以及 **merg** ing 和 **commit** ting，等等……但是，等等！它实际上是如何工作的？

# git 的核心是什么？

嗯，GIT 只是一个持久的 hashmap:你给他一个对象，他给你一个对应于那个对象的 hash。就是这样。很简单，对吧？

```
$ echo "something" | git hash-object --stdin
deba01fc8d98200761c46eb139f11ac244cf6eb5
```

GIT 一直在这样做:它读取内容，存储内容，并基于内容生成散列**。**

这意味着**相同的生成哈希==相同的内容**。这是*非常非常重要的*，因为 git 不喜欢重复，而*从不*多次存储具有相同内容(和相同散列)的对象。

好吧，但是这些*物体*到底是什么？

对象是一段内容:它可以是文件的内容，也可以是目录结构。对象是 git 可以存储的任何东西。

让我们尝试将字符串“something”存储到 GIT 中。

```
$ echo "something" | git hash-object --stdin -w
fatal: Not a git repository (or any of the parent directories): .git
```

一个错误…太好了！

git 抱怨这个文件夹“不是一个 GIT 存储库”。

![](img/22ed6f63d92da0e7af75e00731f9cb0d.png)

# 什么是 git 存储库？

嗯，GIT 存储内容，所以，从某种角度来说，GIT 是一个文件系统。和其他文件系统一样，它也需要初始化。

```
$ git init
Initialized empty Git repository
```

与其他文件系统不同，GIT 使用文件来存储对象，这不是磁盘上的底层结构，但非常相似。

现在我们已经初始化了 GIT 存储库，我们准备好存储对象了:

```
$ echo "something" | git hash-object --stdin -w
deba01fc8d98200761c46eb139f11ac244cf6eb5
```

已存储。万岁！

现在我们需要一种方法来检索那个对象。这很简单:

```
$ git cat-file -p deba01f
something
```

git cat-file 是一个强大的文件。与标志`-p`一起使用，它打印对象并退出，但是我们可以使用标志`-t`向它询问对象的类型。在这种情况下，它会回答说该对象属于类型`blob`，一个二进制大对象，一个用户内容。

是的，是的，你是对的:以这种方式存储和检索对象是非常不舒服的，而且我们没有对任何东西进行版本控制。我们需要更简单的东西…

哦，我有主意了！让我们把文件转换成对象！

太好了！我们还可以存储文件夹和所有的文件树！让我们开始吧。

```
$ echo "something" > README
```

好了，我已经创建了一个文件，现在呢？让我们问一下 GIT(使用 **git 状态**)

```
$ git status
On branch masterInitial commitUntracked files:
  (use "git add <file>..." to include in what will be committed) READMEnothing added to commit but untracked files present (use "git add" to track)
```

哇哦。嘿，等等。那是什么？什么是提交？一根树枝？我们在说什么？

![](img/0ad366e71566aee6b0900e00bfe9e041.png)

# 承诺

我们稍后会谈到分支；对于提交是什么，我可以告诉你，这是创建提交时文件和文件夹所处状态的快照。

因此，我们这里需要的是创建一个提交。

Git 使用两个阶段的过程来创建提交:在第一个阶段中，它将文件的快照创建到他的“暂存区”中，在第二个阶段中，它将这些快照存储到一个提交对象中。

做起来比说起来容易:让我们将自述文件添加到临时区域

```
$ git add README
$ git status
On branch masterInitial commitChanges to be committed:
  (use "git rm --cached <file>..." to unstage)new file: README
```

好，补充！`git status`显示文件已经被添加到 stage 中(“Changes to commit”听起来不错，对吧？)

让我们保存这些更改:

```
$ git commit -m "Added README"
[master (root-commit) aeb27a2] Added README
1 file changed, 1 insertion(+)
create mode 100644 README
```

干得好！我们已经创建了第一个提交！但是…你能看到吗？`aeb27a2`似乎是一个对象哈希。

让我们看看里面有什么！

```
$ git cat-file -p aeb27a2
tree a8986e4af597c38dd3bb1ec08bea46cb123a929e
author Alessandro Chitolina <alekitto@gmail.com> 1614506381 +0100
committer Alessandro Chitolina <alekitto@gmail.com> 1614506381 +0100Added README
```

“添加自述文件”是我几分钟前刚刚写的，但其他信息呢？

“作者”和“提交者”看起来很简单:那就是我。并且该数字必须是创建提交时的时间戳。

但是“树”呢？似乎是一个物体…好吧，让我们检查一下！

```
$ git cat-file -p a8986e4af597c38dd3bb1ec08bea46cb123a929e
100644 blob deba01fc8d98200761c46eb139f11ac244cf6eb5 README
```

惊喜惊喜！树是 GIT 上的“文件夹”表示。只有一个名为“README”的文件，存储为对象`deba01fc8d98200761c46eb139f11ac244cf6eb5`(我以前听过这个数字，对吧？)，拥有权限`100644`(或者只是`0644`)和类型`blob`(“二进制大对象”的首字母缩写，所以它是一个具有*真实*内容的文件)。

现在，我们可以通过新的提交来创建和添加更多的文件(或文件夹)，或者修改现有的文件，但是即使在这种情况下，这个`commit`对象也将保持不变，并且将总是包含具有相同文件表示的相同树对象指针，这将确保不会及时丢失任何数据。

第一部分结束。在第二部分，我将和你谈谈分支，远程和一些有用的 git 命令。

感谢阅读。
答