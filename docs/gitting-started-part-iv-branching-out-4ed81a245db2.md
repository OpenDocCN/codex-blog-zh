# Gitting Started(第四部分):拓展业务

> 原文：<https://medium.com/codex/gitting-started-part-iv-branching-out-4ed81a245db2?source=collection_archive---------10----------------------->

欢迎阅读我的 Git 入门系列的第四部分！在这篇文章中，我将带您创建一个新的分支，提交这个分支，然后将这个分支合并回基本分支。到目前为止，在本系列中，我已经向您介绍了 Git 如何工作的概述，向您展示了如何在新的或现有的目录中创建新的 Git 存储库，并帮助您在新的 repo 中进行第一次提交。如果这些事情对你来说听起来很陌生，我鼓励你在继续这篇文章之前阅读或重读那些文章。

正如我在其他帖子中提到的，这些帖子假设您正在使用基于 UNIX 的操作系统，如 OS X 或任何 Linux 发行版，并且您对终端和系统上可用的命令行实用程序有基本的了解，如`cd`、`ls`、`pwd`、`rm`等。

我将在本文中讨论的 Git 命令有:

*   `git branch`
*   `git checkout`
*   `git diff`
*   `git merge`
*   `git restore`

我还将为`git commit`介绍几个新选项。

# Git 分支:综述

每个 Git 回购都有一个或多个分支。当您使用`git init`创建 git repo 时，默认情况下它有一个分支，称为`master`。(出于包容性的原因，行业标准正在演变为使用`main`而不是`master`，但是 Git 继续使用`master`作为默认设置，不幸的是，这种情况可能会持续相当长一段时间。出于这个原因，我在这个系列中也使用`master`作为默认基础分支的名称。在这里你可以找到如何将你的`master`分支重命名为`main`的[说明](https://www.git-tower.com/learn/git/faq/git-rename-master-to-main/)，我鼓励你考虑这么做。对于这篇文章和本系列文章来说，这样做的唯一含义是，只要`master`出现在命令中，就需要用`main`替换它。)

通常，`master`上的代码表示应用程序的当前部署状态或代码的最新版本。因此，最好不要直接在`master`上做新的开发工作。相反，您可以创建一个新的分支，在那里进行更改，并且当您确定它们工作并且没有破坏任何东西时，将来自那个分支的修订合并到`master`中。

可以使用任何现有的分支作为基础创建一个新的 Git 分支。然而，你最常使用的是`master`作为你的基本分支。当您创建一个新分支时，它最初将与基本分支相同。但是，在新分支上进行的新提交不会出现在基本分支上。同样，如果在您处理新分支时对基础分支进行了额外的修订，那么基础分支上的修订将不会出现在您的分支上。这是 Git 的一个强大特性，它使多个开发人员能够同时处理同一个代码库。

该图显示了一个进展，其中创建了一个新分支`my-branch`，以`master`为基础分支，然后创建了一个`my-other-branch`，以`my-branch`为基础分支。几经修改后，`my-other-branch`合并为`my-branch`。之后，`my-other-branch`的所有修订也会出现在`my-branch`上。(`my-other-branch`在此之后可以删除。)然后，`my-branch`合并成`master`。此时，插图中显示的所有修订都出现在`master`上。

![](img/0887955472a1cf5d934c998c234982eb.png)

图 git 中的分支和修订

本系列的[第一部分](https://dana-scheider.medium.com/gitting-started-part-i-a-total-beginners-intro-to-git-761715f5cc13)给出了更多信息和该图的逐步解释。第一部分还介绍了当基础分支上的修订与被合并的另一个分支上的修订冲突时会发生什么。

# 查看现有分支

要查看您的存储库中当前有哪些分支，只需从 repo 中的任何目录运行该命令(即，`.git`目录所在的目录或其任何子目录):

```
$ git branch
```

您将看到一个分支列表。如果您在第二部分创建的回购上运行`git branch`，您将看到一个分支:`master`。

如果您的 repo 有多个分支，Git 会用一个星号表示您当前所在的分支(或者，用 Git 语言来说，您当前已经“签出”了哪个分支)。在本例中，您可以看到有三个分支，`master`、`my-branch-1`和`my-branch-2`，并且您目前从`master`开始工作:

```
* master
  my-branch-1
  my-branch-2
```

# 创建新分支

要创建一个新的分支，您可以使用带有`-b`选项的`git checkout`命令来指定新分支的名称。`git checkout`是一个你会经常使用的灵活命令，这只是它的用途之一。

给新分支机构命名时，选择一个能描述你要在这个分支机构做什么的名称。在这种情况下，我们将创建一个新分支来添加一个功能，用户可以在其中指定应用程序向谁问好，因此我们将通过运行以下命令来创建它:

```
$ git checkout -b allow-user-input
```

如果一切顺利，我们将看到输出:

```
Switched to a new branch 'allow-user-input'
```

从现在开始，我们提交的任何更改都将只影响`allow-user-input`分支。他们还不会出现在`master`上。

# 在新的分公司工作

在新的分支上工作和在`master`上工作是一样的。在这种情况下，我们将看到一些不同之处，因为我们将修改 Git 已经在跟踪的文件，而不是像第三部分那样向 Git 添加一个新文件。

在您选择的文本编辑器中，打开文件`hello_world.rb`。目前，该文件只有一行:

```
puts 'Hello World!'
```

这是将单词`Hello World!`打印到控制台的 Ruby 代码。我们将修改它，让它提示用户输入一个名字，然后向那个人问好。在文本编辑器中，将文件内容更改为以下内容，然后保存:

```
puts 'Please enter a name:'
name = gets.chomp
puts 'Hello ' + name + '!'
```

如果您运行这个命令，您将看到提示您输入一个名称:

```
$ ruby hello_world.rb
```

当你输入一个名字，比如“Greg”，程序会输出`Hello Greg!`到控制台。很整洁，是吧？

现在我们有了这个新特性，我们要确保将它提交到 Git 历史中。首先，我们将运行`git status`来查看是否有未分级的变更。`git status`的输出应该是:

```
On branch allow-user-input
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in the working directory)
      modified:  hello_world.rbno changes added to commit (use "git add" and/or "git commit -a")
```

请注意，输出告诉我们，正如所料，我们在`allow-user-input`分支上，并且文件`hello_world.rb`已经被修改。现在，在我们准备提交变更之前，我们要确保我们清楚我们将准备什么变更。为此，我们使用`git diff`命令。运行:

```
$ git diff 
```

您应该看到以下内容:

```
diff --git a/hello_world.rb b/hello_world.rb
index 733c205..7146b79 100644
--- a/hello_world.rb
+++ b/hello_world.rb
@@ -1 +1,3 @@
-puts 'Hello World!'
+puts 'Please enter a name:'
+name = gets.chomp
+puts 'Hello ' + name + '!'
(END)
```

这个输出将在你的终端上被着色，这是非常有用的。我们最感兴趣的部分是在带有`@@ -1 +1,3 @@`的线下面的部分。diff 告诉我们,`puts 'Hello World!`已经从文件中删除，接下来的三行已经被添加。在您的终端中，已移除的线路将以红色显示，并且前面有一个`—`标志。已添加的线将以绿色显示，并带有一个`+`标志。要退出 diff 并返回到您的命令行，您可以按下`Q`。

现在我们确信这些是我们想要提交的更改，我们可以运行:

```
$ git add hello_world.rb
```

我建议再次运行`git status`来验证该文件现在已暂存。接下来，提交更改:

```
$ git commit -m "Accept name as user input"
```

还有另一种更快的方式来提交这些更改。您可以通过运行以下命令来转移和提交所有已修改的文件:

```
$ git commit -a -m "Accept name as user input"
```

如果一切按计划进行，我们将得到以下输出:

```
[allow-user-input 5f38523] Accept name as user input
 1 file changed, 3 insertions(+), 1 deletion(-)
```

这告诉我们已经提交的分支、提交 SHA 的前七位数字(有关提交 SHA 的更多信息，请参见[第一部分](https://dana-scheider.medium.com/gitting-started-part-i-a-total-beginners-intro-to-git-761715f5cc13))和提交消息。在此之下，有关于有多少文件被更改以及这些文件中有多少更改的信息。如果我们添加或删除一个文件，这些信息也会被给出。

按照 UNIX 惯例，您可以将`-a`和`-m`选项组合成:

```
$ git commit -am "Accept name as user input"
```

**将** `**-a**` **标志与** `**git commit**` **一起使用有点冒险，就像** `**git add .**`一样，因为如果有很多修改过的文件，你就冒着提交你并不真正想要的更改的风险。同样重要的是要知道`git commit -a`不会暂存或提交当前没有被 Git 跟踪的新文件——只有那些已经被跟踪并且自上次提交以来已经被修改过的文件。要暂存新创建的文件，您总是需要使用`git add`。

请注意，我们不需要做任何特殊的事情来确保变更被提交到正确的分支——我们只需要确保在提交变更时我们在那个分支上。

# 检查基本分支并合并

既然我们已经添加了漂亮的新特性，我们希望将该特性添加到`master`，它将包含在下一个版本中。为了合并我们所做的修订，我们将首先运行:

```
$ git checkout master
```

这使我们回到了`master`分支。运行这样的命令总是会将您带到您指定的任何分支:

```
$ git checkout branch-name 
```

如果您想要的分支不存在，您需要像上面那样在分支名称前使用`-b`标志，否则您会得到一个错误。

现在，再次运行`git branch`显示有一个名为`allow-user-input`的分支和另一个名为`master`的分支，我们现在在`master`上:

```
 allow-user-input
* master
```

在将特征分支合并到`master`之前，最好先检查特征分支和`master`之间的差异。(由于在`allow-user-input`特性分支上可能有多次提交，以及在我们创建新分支后在主分支上的修改，这个差异不会总是与我们在最后一次提交到`allow-user-input`分支之前看到的差异相同。)要检查这种差异，我们可以运行:

```
$ git diff allow-user-input
```

这将显示出`master`(或者你当前所在的分支)和`allow-user-input`分支之间的区别。通常，无论您在哪个分支上，您都可以通过运行以下命令来查看任意两个分支之间的差异:

```
$ git diff branch-1 branch-2
```

即使两个分支都不依赖于另一个分支，这也是可行的。

在这种情况下，我们看到的`master`和`allow-user-input`之间的差异与我们刚刚看到的差异相同。这正是我们所期待的，所以我们准备合并。我们通过运行以下命令来实现:

```
$ git merge allow-user-input
```

该命令会将指定分支上的修订合并到您当前已签出的分支中，因此在运行该命令之前，请确保您位于要合并到的分支上。当您运行`git merge`命令时，您应该会看到以下输出(提交 sha—`106b5ea`和`5f38523` —会有所不同):

```
Updating 106b5ea..5f38523
Fast-forward
 hello_world.rb | 4 +++-
 1 file changed, 3 insertions(+), 1 deletion(-)
```

`4 +++-`表示你修改了文件中的四行，增加了三行，删除了一行。请注意，Git 会将对代码行的任何更改解释为一次删除(原始行)和一次插入(修改后的行)。

合并只是单向的:来自`allow-user-input`的修订现在会出现在`master`上，但是`allow-user-input`分支在合并后仍然会保持不变。

# 删除分支

如果你愿意，合并后，你可以在特征分支上做更多的工作，合并到`master`或任何你喜欢的基础分支。记住，在`allow-user-input`创建后对`master`所做的修改不会出现在`allow-user-input`分支上，即使分支合并后也是如此——如果你想让它们出现在那里，你必须要么将主文件合并到`allow-user-input`中，要么使用`git rebase`。重新定基可能有点冒险，因为它修改了 Git 历史，所以现在坚持合并。

一般来说，一旦你合并了一个分支，你就不再需要它了。拥有太多的 Git 分支会变得很烦人，因为当你运行`git branch`时，有一个不断增长的列表，你必须浏览它才能找到你感兴趣的分支。因此，您可能希望在合并分支后删除它们。

一旦您签出了`master`并合并了`allow-user-input`，您就可以运行这个来删除分支:

```
$ git branch -d allow-user-input
```

您应该会看到如下所示的输出:

```
Deleted branch allow-user-input (was 5f38523)
```

`5f38523`是`allow-user-input`分支上最后一个提交 SHA 的前七位数字，它们在你的机器上和在我的机器上是不同的。就是这样！您的分支已被删除。

有时，您可能想要删除尚未合并的分支。如果您运行`git branch -d unmerged-branch`，Git 将通过引发一个错误来避免永久丢失在该分支上所做的更改。错误的输出如下所示:

```
error: The branch 'unmerged-branch' is not fully merged.
If you are sure you want to delete it, run 'git branch -D unmerged-branch'.
```

如果您完全确定不再需要那个分支上的修订，正如输出所说的，您可以用以下命令删除它:

```
$ git branch -D unmerged-branch
```

但是要小心——在您运行`git branch -D`之后，在被删除的分支上的任何未合并的变更将永远消失，并且在那之后没有办法恢复它们。在删除分支之前，您总是可以通过运行以下命令来验证有哪些更改:

```
$ git diff master unmerged-branch
```

# 结论

在这一点上，我认为是时候回顾一下了。如果您一直在关注这一系列文章，那么您现在已经了解了相当多的 Git 功能，您将在职业生涯中每天使用这些功能。您已经学会了使用`git init`创建 git 存储库，使用`git add`准备您的第一次更改，并使用`git commit`将您的代码版本永久保存在 Git 中。

此外，您还了解了 Git 修订(或“提交”)和分支——如何创建一个新分支，找出存在哪些分支，确定您在哪个分支上，以及使用`git branch`和`git checkout`切换分支。您已经学会了当您确定它们已经准备好投入生产时合并变更，当您不再需要它时删除分支。给自己一个鼓励！

不过，这仍然只是 Git 所能提供的强大功能的一小部分。版本控制最令人信服的目的可能是让您在出错时快速修复。在[第五部](https://dana-scheider.medium.com/gitting-started-part-v-when-sh-t-goes-south-9b15a0882d4a)中，我将带你了解`git revert`、`git restore`和`git reset`的基本知识以及`git checkout`的其他用法。