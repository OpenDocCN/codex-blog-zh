# 掌握终端:你需要知道的一切。

> 原文：<https://medium.com/codex/mastering-the-terminal-all-you-need-to-know-cd9fdad93c8b?source=collection_archive---------1----------------------->

![](img/aeb5c6b26209d04ecf5b48b0d75ff083.png)

你好。如果你是第一次接触*终端*，或者如果你曾经觉得使用它不舒服，或者如果你想知道学习这些对你有什么好处……那么你来对地方了。在这篇文章中，我将带你通过基本的*终端*命令，让你开始。

出于一些奇怪的原因，我发现命令行有这种耻辱，大多数人认为他们可以用基于 *GUI* 的应用程序完成工作，比如 Finder(在 mac 中),而不用费心去学习终端命令。但是我在这里要说的是，一旦你越过了学习曲线，使用命令行比你想象的要容易和快捷得多，在这种情况下，学习曲线并不是很高。如果你是一名软件开发人员，你可能会遇到一些情况，在这些情况下你没有其他选择，只能使用老式的命令行。不管怎样，在本文结束时，您将会对许多基本的 unix 命令很熟悉。但是首先，您应该明白为什么使用命令行对您更有好处，而不是仅仅相信我的话。

那么*为什么*要掌握命令行呢？

至于为什么，有无数的理由。但是这里有一些，

1.  更好地控制您的机器。

有了命令行，你可以做很多事情，否则是不可能的。您可以更改文件权限、查看隐藏文件(使用 finder 无法做到这一点)、启动服务器等等。基本上，学习命令行将加深你与你的机器的关系，它将*总是*派上用场。

2.这比使用任何 GUI 工具都要快得多。

这将节省大量时间，只需一行代码就能让事情按照你想要的方式进行。例如，如果你想创建一个嵌套的文件夹结构，如果你不使用命令行，这将花费你很长时间。只需通过终端执行一行命令就可以创建这样的嵌套文件夹和文件结构。打字比点击和移动鼠标要快得多。

3.您可以自动化许多任务。

只需一个命令就可以完成重复的任务。如果你知道如何随心所欲地使用终端，你就不必忍受那些体力劳动的负担。

如果您想为一年中的每一天创建一个包含子文件夹的文件夹，想象一下右键单击并创建一个新文件夹 365 次，然后用日期号重命名每个文件夹的痛苦。但这只是终端的一个命令任务，

```
mkdir Day{1..365} 
```

如果您从终端执行这个命令，突然之间您将拥有 365 个文件夹，每个文件夹都以 Day1、Day2 等命名。

4.这些命令是通用的。

你可以在你的 ubuntu 机器上使用任何 unix 命令，没有任何问题，只需要在 windows 机器上做一点点工作。

5.如果你打算成为一名 *web 开发人员*、*数据科学家*、 *devops 工程师*、*云工程师*或任何与软件相关的工作，掌握命令行真的会有回报。当使用像 *git* 这样的版本控制系统时，这也会派上用场。对终端命令有一个很好的了解会让你在开发之旅中更上一层楼。

现在您已经知道了掌握终端的重要性，在我们开始学习终端命令之前，我们有一些基础工作要做。那就是了解*操作系统*的多样性。

# 操作系统。

基本上所有的操作系统都可以大致分为两类。

1.  微软 NT 的后代，如 Windows，XBox 等。
2.  所有其他操作系统几乎都可以追溯到 Unix。这包括 Mac OS、Linux、Android、Chrome OS 等。

如果你有兴趣了解*操作系统家族*，我强烈推荐你查看[这篇](https://eylenburg.github.io/os_familytree.htm)文章。但是现在，知道两个不同的操作系统家族以及你的机器属于哪一个就足够了。我将要详述的所有命令都是将在 mac 和 ubuntu(T21)上运行的 Unix 命令，Ubuntu 是一个完整的 linux 操作系统。

在 Windows 中，由于操作系统系列来源不同，可能会有不同的变化。但是这些命令大多是通用的，这意味着掌握它们将使你在跨平台方面受益匪浅。

# Unix 和 Unix 哲学。

> Unix 是一个由多任务处理、多用户计算机和操作系统组成的家族，这些操作系统是由最初的 Unix 操作系统衍生而来的，这些操作系统是在 20 世纪 70 年代由肯·汤普森、丹尼斯·里奇等人在贝尔实验室的研究中心开发的。

嗯，这就是维基百科对 Unix 是什么的描述。 *Unix* 基本上是我们今天拥有的许多不同操作系统的祖先。除了 windows NT 的后代，几乎所有其他操作系统都是直接或间接从 *Unix* 发展而来的。

在计算机早期，*操作系统*与*硬件*紧密绑定。Unix 将这两者分离，并强调模块化软件设计，简单来说就是创建小的独立程序，这些程序可以聚集在一起执行复杂的任务。

在这篇文章中，我们不打算学习 Unix 的详细历史，但是如果你感兴趣，你可以看看这篇文章。

我们将要探索的命令是与真正的 unix 操作系统兼容的命令，如 mac 或 ubuntu 等 Unix 操作系统。

好吧！现在是时候使用命令了！

# 您需要知道的基本 Unix 命令。

1.  “pwd”命令—打印工作目录命令

如果你现在打开你的终端，输入“pwd ”,然后回车，你就可以知道你现在在哪个目录。这是有史以来最常用的命令之一。根据你目前的位置，你会得到类似这样的东西。

```
/Users/navodanilakshi
```

无论您在哪里，从*根目录*到您的*当前工作目录*的路径都会被打印到*标准输出*中。请注意，路径以“/”开头，它代表根目录。在我的例子中，我在我的主目录(navodanilakshi)中，该目录位于根目录下的 Users 中。

2.ls 命令

这可能是您遇到的最常用的命令。如果端子标签打开，输入 *ls* 并按回车键。这将列出当前工作目录中的内容。但默认情况下，它不会列出隐藏文件。如果你想列出所有的文件，包括以“.”开头的文件比如。gitignore，。bash_profile，您需要向 *ls* 命令传递一个额外的参数，即 *-a* 。

```
ls  #this will list directory contentsls -a  #this will list directory contents including hidden filesls -l   #this will list in long format
```

当您使用 *ls -l，*时，您会注意到文件大小显示为 128，96。这些不是很容易让人读懂。要检索可读格式的文件大小，如 128KB、96KB，您可以在执行 ls -l 命令时添加-h，它看起来会像这样。

```
ls -lh 
```

有很多像 *l，a，h* 这样的参数，你可以在破折号后传递到 *ls* 命令中，但是这些是最常用的。

3.“cd”命令

```
cd <destination>
```

简单地说，这是用来*改变目录到一个指定的位置*。如果你想进入某个目录，你可以执行 *cd* 命令，命令后面是你想去的地方的路径。该路径可以是相对的或绝对的(这意味着从根目录开始)

如果路径是绝对的，那么它可以把你从系统中的任何地方带到那个地方。相对路径不是这种情况。这完全取决于你在哪个目录。

如果你执行 *ls* 命令并看到你想要导航到的目录，那么这意味着它在你当前的工作目录中。在这种情况下，您可以简单地使用 *cd* 后跟目录名。您不需要指定路径，因为它在同一目录中。

```
cd projects 
```

以下命令将带您到*项目中的*公共*文件夹。*

```
cd projects/public
```

如果您想从计算机上的任何位置导航到根目录，可以使用，

```
cd /
```

如果您想从电脑上的任何位置导航到主目录，您可以使用，

```
cd ~
```

如果您想要备份一个级别(转到上一级目录)您可以使用，

```
cd ..
```

4.“mkdir”命令

mkdir 实用程序按照指定的顺序创建名为操作数的目录。您可以在当前工作目录中创建一个文件夹，只需在 mkdir 后指定文件夹名称。

```
mkdir cats  #this will make a cats folder inside your directorymkdir cats/kittens #this will make a kittens folder inside catsmkdir cats dogs rats #this will make 3 folders
```

如果您想创建一个文件夹，并在新创建的文件夹中创建另一个子文件夹，可以通过在文件夹结构前添加-p 来实现。

```
mkdir -p mainFolder/subFolder
```

5.“触摸”命令

该命令用于*制作文件*。这非常类似于 mkdir 命令，但是它不能用来创建文件夹。这是为了制作文件。

```
touch newfile.txt  #This will create a newfile.txt  inside of your current working directorytouch projects/newfile.txt -> This will create a newfile.txt  inside of projects folder
```

您也可以使用 touch 命令一次创建多个文件。

```
touch index.html app.js app.css -> This will create 3 new files(index.html,app.js,app.css)inside of your current working directory
```

6.“rm”命令

该命令用于删除文件或文件夹。它通常用于以下几个方面:

```
rm <filename>
```

但是您指定文件应该在您当前的工作目录中，否则您应该指定路径:例如:rm projects/new/hello.txt

此外，您*不能*仅使用 rm 后跟文件夹名称来删除文件夹。普通 rm 命令将*仅*用于文件。因此，如果你打算删除文件夹，那么你应该添加*额外的参数*,让它在 rm 中成为可能。

```
rm -d <foldername>  #This will delete projects folder is it’s emptyrm -r <foldername> #this will recursively delete the specified folder if it’s not empty along with any other files folders inside it.
```

7.“mv”命令

此命令通常用于将文件从一个目标移动到另一个目标。

```
mv <file> <destination>
```

如果您指定的目的地*不存在*，那么*会将*文件重命名为指定的目的地名称。这使得使用 *mv* 命令来移动文件和重命名文件成为可能。

```
mv <current> <new>  #can be used to rename a filemv new.txt ~  #this will move new.txt which is in your current working directory to home directorymv <file> ..  #this can be used to move an item to the previous directory
```

8.cp '命令

此命令可用于将文件复制到给定的目标。

```
cp <source> <destination>
```

示例:

```
cp todos moreTodos #this makes a copy of todos called moreTodos in the same locationcp todos ~/newTodos #this makes a copy of todos called newTodos in the home directory 
```

如果你正在复制目录，添加 *-r* 到*递归地*复制目录中的任何内容。

```
cp -r <folder> <destination>
```

是时候像专业人士一样卷起袖子编写终端命令了。干杯！