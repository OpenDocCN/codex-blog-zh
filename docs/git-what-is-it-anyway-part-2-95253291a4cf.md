# 吉特:这到底是什么？第二部分

> 原文：<https://medium.com/codex/git-what-is-it-anyway-part-2-95253291a4cf?source=collection_archive---------10----------------------->

## [法典](http://medium.com/codex)

## git 内部是如何工作的，为什么这么棒？

[在第一部分](/p/git-what-is-it-anyway-7fe34d40ae0e)中，我们讨论了 GIT 核心:哈希、对象、树、blobs 和其他底层基础。

![](img/486eee384b225875e5bb321e15c7dc1f.png)

[Yancy Min](https://unsplash.com/@yancymin?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

现在我们来看看这些对象是如何在高级命令中使用的。

## Git:主流之前的区块链

在第一部分中，我们创建了一个提交来获取我们代码的不可变快照，但是有趣的部分是提交像在一个链中一样相互链接:每个提交都包含一个对其父(或多个父)的引用，这是保证它们不可变的魔术。

一个例子胜过千言万语，所以让我们创建一个新文件，将其添加到临时区域，并创建一个提交对象，如第 1 部分所示:

```
$ echo "another file" > NEWFILE.txt
$ git add NEWFILE.txt
$ git commit -m "added new file"
[master 031b65d] added new file
 1 file changed, 1 insertion(+)
 create mode 100644 NEWFILE.txt
```

简单吧？现在让我们试着检查这个新的提交对象(`031b65d`)

```
$ git cat-file -p 031b65d
tree 2eb34818fda8b4fbd27371f07988b5944d11b047
parent aeb27a2953068137e0d205b0ed21834607ccd56d
author Alessandro Chitolina <[alekitto@gmail.com](mailto:alekitto@gmail.com)> 1614982391 +0100
committer Alessandro Chitolina <[alekitto@gmail.com](mailto:alekitto@gmail.com)> 1614982391 +0100added new file
```

这个提交对象上存在一个新的`parent`属性。`aeb27a2`你还记得吗？当然可以！这是我们在第 1 部分中创建的第一个提交！

如果父节点中的某些内容发生了变化，那么它的哈希值就会不同，所有子节点的哈希值也会不同。这个简单的事情保证了提交的完全不变性。

![](img/571a78113858562924deeeb5fad1bc9f.png)

马修·兰卡斯特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 分支:对提交链的简短引用

好吧，好吧，但是在日常工作中总是使用这些长散列并不方便，我们需要更短更有意义的引用来提交…

女士们，先生们，很荣幸为您介绍*分公司。*我们在创建提交时已经遇到了一个，它的名字是`master`，这是 git 中的默认分支。

我们所说的“分支”只是对提交的引用，即分支中的最新提交。我们可以看到它正在读取`.git`目录中的一个文件:

```
$ cat .git/refs/heads/master
031b65d4cca27bcb2063214a440d12141220cfa1
```

你能看见它吗？与我们刚刚创建的最后一次提交的散列完全相同！

当我们调用`git commit`时，我们不仅创建了一个 commit 对象及其所有相关的`tree`和`blob`对象，而且我们还更新了分支头引用。

这个引用和散列系统是 git killer 特性的基础，它与其他版本控制系统有着真正的区别:它可以在一眨眼的时间内创建分支。

```
$ git branch test-branch
```

立即为`test-branch`分支创建一个新的引用。无需复制文件和文件夹、修复链接、权限和其他 I/O 繁重的操作，只需创建对`HEAD`提交的引用。

![](img/d1f1c92e80738e3bb952455a6c31b0ac.png)

赫拉克勒斯和九头蛇

## 精彩的参考文献

一个名为`HEAD`的特殊引用总是出现在 git 存储库中，它指向最新的提交，我们正在处理的那个，以及在新创建的提交中被称为`parent`的那个。

但是像 Hydra 一样，git 存储库有许多头，每个本地分支一个，每个远程分支一个(我们将在第 3 部分讨论远程)，加上特殊的`HEAD`引用。

我们可以随时使用`checkout`命令更改`HEAD`，有效地在分支、标签、提交和其他引用之间切换。

我们之前已经创建了`test-branch`分支，但是分支的简单创建并没有改变`HEAD`。要切换到该分支，我们需要执行:

```
$ git checkout test-branch
Switched to branch 'test-branch'
```

相当简单！从现在开始，我们创建的每一个提交都将更新`test-branch`头的引用。

```
$ echo "branched" > BRANCHED.txt
$ git add BRANCHED.txt
$ git commit -m "commit on a branch"
[test-branch 83b1774] commit on a branch
 1 file changed, 1 insertion(+)
 create mode 100644 BRANCHED.txt
```

现在我们所做的修改只出现在由`test-branch`引用的提交中。我们可以切换回`master`分支进行检查。

```
$ git checkout master
Switched to branch 'master'
$ ls
NEWFILE.txt README$ git log
commit 031b65d4cca27bcb2063214a440d12141220cfa1
Author: Alessandro Chitolina <[alekitto@gmail.com](mailto:alekitto@gmail.com)>
Date:   Fri Mar 5 23:13:11 2021 +0100added new filecommit aeb27a2953068137e0d205b0ed21834607ccd56d
Author: Alessandro Chitolina <[alekitto@gmail.com](mailto:alekitto@gmail.com)>
Date:   Sun Feb 28 10:59:41 2021 +0100Added README
```

如您所见，`83b1774`提交并不存在，比如`BRANCHED.txt`文件。我们现在可以用另一个文件创建一个新的提交。

```
$ echo "this is only on master" > MASTER.txt
$ git add MASTER.txt
$ git commit -m "commit only on master"
[master edde583] commit only on master
 1 file changed, 1 insertion(+)
 create mode 100644 MASTER.txt
```

![](img/45858a622c6841b3c4064ff7e8cee017.png)

合并道路

## git 合并:神奇地合并分支

现在这两个分支正在分开。在`031b65d`之前，它们共享相同的提交，但是随后从那里创建了两个不同的提交。

```
 * commit on a branch (83b1774)
  |
* commit only on master (edde583)
|/
* added new file (031b65d)
|
* Added README (aeb27a2)
```

在团队中工作时，这是一种常见的情况，因为每个团队成员都处理一个特性，创建和更新自己的分支，直到工作完成。
在工作结束时，功能分支必须与主开发分支相结合(`master`)。

为此，我们必须使用`git merge`命令。该命令不仅将当前头与另一个头组合在一起，还创建了一个新的 commit 对象，它有两个父对象，每个合并的头一个。

```
$ git merge test-branch --no-edit
Merge made by the 'recursive' strategy.
 BRANCHED.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 BRANCHED.txt
```

万岁！我们成功合并了两家分公司！我们也可以用旧的好的`cat-file`命令来检查合并提交

```
$ git cat-file -p HEAD
tree 9f7377525008d0846feb8a2bb3aa0278b81f5927
parent edde583143be80ccb88b5495436ceb1998d395e0
parent 83b17747d65b40c50fa07e2e28dd04bf7cd4e8c0
author Alessandro Chitolina <[alekitto@gmail.com](mailto:alekitto@gmail.com)> 1614991406 +0100
committer Alessandro Chitolina <[alekitto@gmail.com](mailto:alekitto@gmail.com)> 1614991406 +0100Merge branch 'test-branch'
```

我们有两个父级(只有合并提交有多个父级)，但是所有的都和标准提交一样，对吗？

图表日志更清楚地向我们展示了情况

```
* Merge branch 'test-branch' (d963718)
|\
| * commit on a branch (83b1774)
| |
* commit only on master (edde583)
|/
* added new file (031b65d)
|
* Added README (aeb27a2)
```

第二部分结束。在第 3 部分中，我们将讨论 rebase、remotes 和一些有用的 git 命令。

感谢阅读，
A。