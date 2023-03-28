# Gitting 开始(第三部分):提交您的第一批文件

> 原文：<https://medium.com/codex/gitting-started-part-iii-committing-your-first-files-da49231db939?source=collection_archive---------15----------------------->

在这篇文章中，我将带您完成将您的第一批文件提交到您在本系列第二部分中创建的 Git 存储库中。(如果您需要 Git 是什么或如何工作的概述，请阅读[第一部分](https://dana-scheider.medium.com/gitting-started-part-i-a-total-beginners-intro-to-git-761715f5cc13)。)要继续学习，您需要您在第二部分中创建的空的 Git 存储库。

这篇文章将介绍三个 Git 命令:

*   `git status`
*   `git add`
*   `git commit`

这些命令都非常灵活，有许多选项可供您选择。然而，由于这是一个初学者的指南，我在这里只涉及其中的几个。有关所有可用选项的更多信息，您可以通过运行`man git-<command>`来使用每个命令的手册页，例如:

```
$ man git-commit
```

与本系列中的其他文章一样，本文假设您正在使用 Linux 或其他基于 UNIX 的操作系统(包括 Mac OS X ),并且对在您的系统上使用命令行有基本的了解。您将在这篇文章中使用的非 Git 命令是:

*   `ls`:列出当前工作目录或其他目录(如果指定)的内容。
*   `cd`:将当前工作目录切换到指定目录。如果未指定目录，会将当前工作目录更改为登录用户的主目录。
*   `echo`:将指定的文本打印到用`>`表示的文件，如果没有指定文件，则打印到控制台。(例如，`echo "foo" > foo.txt`将文本`foo`写入名为`foo.txt`的文件中。如果文件不存在，将会创建该文件。如果它确实存在，其内容将被覆盖。)

# 查看具有 git 状态的新文件

为了用 Git 跟踪修订，您需要给 Git 一些要跟踪的修订！首先运行以下命令，将当前工作目录更改为您的存储库:

```
$ cd my_repo
```

用您的存储库的名称或路径替换`my_repo`。接下来，通过运行以下命令为 Git 创建一个要跟踪的文件:

```
$ echo "puts 'Hello World!'" > hello_world.rb
```

现在你有了一个用 Ruby 语言编写的文件，它将单词打印到控制台。要测试它，您可以运行:

```
$ ruby hello_world.rb
```

现在您有了新文件，您可以通过运行

```
$ git status
```

您的输出将如下所示:

```
On branch masterNo commits yetUntracked files:
  (use "git add <file>..." to include in what will be committed)
      hello_world.rbnothing added to commit but untracked files present (use "git add" to track)
```

请注意，您的新文件列在`Untracked files`下。在`Untracked files`下面列出的文件是您还没有告诉 git 跟踪的文件。每当您创建一个新文件时，您需要明确地告诉 Git 您想要跟踪它。

如果您想要更多关于`git status`命令的信息，运行`man git-status`(不要忘记连字符！).

# 使用 git add 暂存新文件

为了让 git 跟踪您的文件，您需要首先通过运行以下命令**暂存**:

```
$ git add hello_world.rb
```

现在又跑`git status`了。您应该会看到以下输出:

```
On branch masterNo commits yetChanges to be committed:
  (use "git rm --cached <file>..." to unstage)
      new file:  hello_world.rb
```

请注意，您的文件现在列在`Changes to be committed`下。如命令输出所示，如果您错误地转移了文件，您可以通过运行以下命令来撤消该操作:

```
$ git rm --cached hello_world.rb
```

假设你有多个文件想一次全部保存。为此，您可以指定多个文件，文件之间留有空格，例如:

```
$ git add hello_world.rb goodbye_cruel_world.rb
```

您还可以运行此命令，将所有新的或已更改的文件转移到存储库中:

```
$ git add .
```

但是，不建议这样做，因为这可能会导致犯下你不想犯的错误！最好仔细检查这些文件，以确保您只存放您想要提交的文件。

我将在以后的文章中更详细地介绍`git add`命令及其选项。同时，如果您想提前阅读，可以通过运行`man git-add`查看手册页。

# 提交暂存文件

现在您的文件已经暂存，您可以**提交**。运行此命令将提交当前转移的所有文件:

```
$ git commit
```

一旦提交，该文件将以其当前形式永久*添加到 git 历史中。即使您随后提交了其他更改，在此修订中所做的更改仍可在将来查看或恢复。不会提交已更改或添加但尚未暂存的文件。这就是您在提交文件之前必须暂存文件的原因——有时您会希望单独提交不同的更改，以便每个修订代表一组特定的逻辑更改。

运行`git commit`时，默认情况下`vim`会打开。`vim`是一个运行在终端上的文本编辑器。(您也可以配置 git 使用您选择的文本编辑器，但是现在我们将坚持使用`vim`。)它打开以便您可以编写一个**提交消息**。你的提交信息应该是对你所做的改变的简要解释。如果您或将来查看提交历史记录的其他人可能不太清楚，您也可以解释这些更改是什么。Git 将提交消息与修订一起保存，这样将来阅读 git 历史的人就可以看到修改了什么以及为什么要修改。

要在`vim`中输入你的提交信息，首先按下`I`，这是你告诉`vim`你想要输入文本的方式。键入您的提交消息，然后按`Esc`。要保存提交消息，请键入`:wq`并按下`Enter`。恭喜你，你第一次提交了！

添加提交消息还有一种更快的方法:在消息后面加上`-m`标志。对于`hello_world.rb`文件，您可以运行:

```
$ git commit -m "Print 'Hello World' to the console"
```

请注意，消息周围需要有引号。

如果一切顺利，您应该会看到以下输出(提交 sha 会有所不同):

```
[master (root-commit) 106b5ea] Print 'Hello World' to the console
 1 file changed, 1 insertion(+)
 create mode 100644 hello_world.rb
```

这个输出告诉您，您在哪个分支上(`master`)、提交 SHA ( `106b5ea`)、更改了哪些文件，以及您是否从该文件中添加或删除了某些内容(如果您更改了文件中的某一行，Git 会将其解释为您删除了原始行并插入了新行)。

# 结论

祝贺您，您已经完成了您的第一次 Git 提交，确保您所做的更改将作为 Git 历史中的一个修订保留下来，以备您将来需要查看或恢复它们。在下一篇文章的[中，我将介绍如何在 Git 中创建新的分支，这样您就可以在存储库中进行额外的修改。](https://dana-scheider.medium.com/gitting-started-part-iv-branching-out-4ed81a245db2)

— — — — — — —

*在某些情况下，提交历史可以被修改或删除，但我们将在后面讨论这些情况。您将无法使用到目前为止所学的命令来修改或删除现有的提交。