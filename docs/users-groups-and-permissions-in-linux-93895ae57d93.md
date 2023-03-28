# Linux 中的用户、组和权限

> 原文：<https://medium.com/codex/users-groups-and-permissions-in-linux-93895ae57d93?source=collection_archive---------2----------------------->

![](img/d4d1b7468614b60fba047d873b5e7d3e.png)

Linux 中的用户、组和权限

用理查德·费曼的话说，“如果你想掌握什么，就去教它。你教得越多，学得越好……”

所以我在这里，在我的掌握之旅中，以迷你教程的形式记录了我如何学会在 Linux 中创建用户、组和权限。

> 我们将使用安装在本地虚拟服务器上的 Ubuntu 但是使用的大多数命令将适用于任何其他 Linux 发行版。本文还假设读者对 Linux 命令有基本的了解。

Linux 操作系统允许多用户功能。这意味着多个用户可以在共享系统资源的同时并发且独立地使用操作系统。

在这个小教程中，我们将创建 3 个组和 15 个用户，并将这些用户分配到 3 个组中。我们还将确保一个组中的用户无法访问另一个组中的文件，除非他们被添加到该组中。

但是首先，让我们来看看 Linux 中不同类型的用户帐户。

## 用户帐户的类型

Linux 用户帐户有三种基本类型:

1.  **Root 用户(管理)**:此用户帐户除了拥有创建、修改和删除用户和组的权限之外，还拥有对系统范围资源的完全访问权限。
2.  **常规用户帐户**:这些是具有登录 shell 和主目录的用户帐户，执行个人使用的任务，通常是独立于用户主目录的常见应用程序任务。
3.  **服务帐户**:这些是分配给应用程序的帐户，但是具有有限的访问权限，以保护应用程序免受潜在的攻击，同时仍然执行基本功能。他们通常没有主目录。

## 创建用户

我们将需要 *admin* 权限来创建用户——然而，出于安全原因，通常避免使用 *root* user 上下文。

首先，按照以下语法使用`useradd`命令创建一个普通用户:

```
$ useradd -m <name-of-user>
```

> **提示**:要了解关于一个 Linux 命令的更多信息，运行`man <command>`，例如，`man useradd`来显示该命令的完整描述。

然后，为该用户分配管理员权限，如下所示:

```
$usermod -aG sudo <username>
```

现在，这个*管理员*用户拥有超级用户权限，不需要用`sudo`命令输入密码。

让我们使用以下命令切换到 *admin* 超级用户:

```
$ sudo su <adminuser>
```

然后用`useradd`命令创建更多的用户:

```
$ sudo useradd -m <name of user>
```

根据您需要创建的用户数量执行此操作—在本例中是 15 个用户。

## 查看用户

系统中的所有用户都存储在`/etc/passwd`目录中。要查看所有用户，请运行以下命令:

```
$ getent passwd
```

这应该会输出类似如下的内容:

```
root:x:0:0:root:/root:/bin/bash
...
user1:x:1002:1002::/home/user1:/bin/sh
user2:x:1003:1002::/home/user2:/bin/sh
user3:x:1004:1002::/home/user3:/bin/sh
user4:x:1006:1003::/home/user4:/bin/sh
user5:x:1007:1003::/home/user5:/bin/sh
...
```

每个条目具有以下特征:

*   用户名:`user1`
*   加密密码:`x`
*   用户的唯一标识符(UID):`100x`
*   用户组 ID (GID): `100x`
*   通用电气综合运营主管(GECOS)字段—在本例中为空。(*该字段包含以逗号分隔的属性字符串形式的一般信息，例如，全名；电话号码；etc)*
*   用户主目录:`/home/user1`
*   用户的默认登录 shell:`/bin/sh`

## **删除用户**

要删除用户，请运行命令:

```
$ sudo userdel -f <user>
```

这将删除所有相关的用户数据，以及用户主目录`-f, --force`中的所有文件。

## 向组中添加用户

为了将 15 个用户添加到组中，让我们创建三个不同的组——一个组为团队领导`leads`，一个组为开发人员`developers`，另一个组为站点可靠性工程团队`sre`——然后将用户添加到组中。

使用以下语法使用`groupadd`命令创建一个组:

```
$ sudo groupadd <groupname>
```

对于我们的例子，我们将创建三个不同的组，如下所示:

```
$ sudo groupadd leads
$ sudo groupadd developers
$ sudo groupadd sre
```

接下来，使用带有以下语法的`gpasswd`命令，将已经创建的用户添加到每个组中，假设每个用户都符合组角色:

```
$ sudo gpasswd -A <user> -M <user,user,user> <groupname>
```

`gpasswd`命令允许您管理组和组成员的创建——`-A`标志定义组管理员，而`-M`标志以逗号分隔列表的形式定义组成员(有关更多详细信息，请参见`man` 页)。

## 查看组

系统中的所有组都存储在`/etc/groups`目录中。要查看所有组，请运行以下命令:

```
$ getent group
```

这将产生类似于以下内容的输出:

```
leads:x:1002:user1,user2,user15
developers:x:1003:user3,user4,user5,user6,user7,user8,user9
sre:x:1004:user10,user11,user12,user13,user14
```

接下来，让我们定义每个组允许的访问级别。

## 权限和所有权

Linux 如此优雅地支持多用户系统的原因是由于*权限。*权限授予用户访问系统内文件和目录的权利。在 Linux 中，基本权限是:

*   ***读*** :打开和查看文件和目录内容的权限。由`r`代表
*   ***写:*** 修改文件内容以及在一个目录内添加、删除和重命名文件的权限。由`w`代表
*   ***执行*** :运行脚本或应用程序的权限。由`x`代表

让我们来演示一下文件权限的意义；使用以下命令创建 3 个单独的文件:

```
$ touch leads.txt developers.txt sre.txt
```

运行以下命令查看文件以及每个文件的当前权限:

```
$ ls -l
```

该命令的输出应该类似于以下内容:

```
-rw-r--r-- 1 adminuser adminuser    0 Aug 15 19:41 leads.txt
-rw-r--r-- 1 adminuser adminuser    0 Aug 15 19:41 developers.txt
-rw-r--r-- 1 adminuser adminuser    0 Aug 15 19:41 sre.txt
```

上述输出中与访问级别相关的部分如下:

*   **前十个字符** `-rw-r--r--`象征文件访问权限:
*   第一个字符表示文件类型，其中`-`表示常规文件。其他的符号有`d`表示目录，`l`表示符号链接，等等。
*   其他九个字符定义了对文件类型的访问权限——前三个字符`rw-`是*用户权限*，接下来的三个`r--` 是*组权限*，而最后三个`r--`是*所有其他用户的权限*。
*   `**root**` **:拥有(创建)文件**的用户
*   `**root**` : **拥有文件**的组

记住上面的信息，我们将把一个特定的组分配给它的相关文件。

## 分配组所有权

我们希望每个文件都属于自己的组。为此，让我们使用`chgrp`(*change group*的简称)命令来分配组所有权。该命令的语法是:

```
$ sudo chgrp <group> <file>
```

因此，对于我们的示例，我们将运行以下命令:

```
$ sudo chgrp leads leads.txt
$ sudo chgrp developers developers.txt
$ sudo chgrp sre sre.txt
```

现在，当我们运行`ls -l`命令时，我们应该得到以下输出:

```
-rw-r--r-- 1 adminuser leads    0 Aug 15 19:41 leads.txt
-rw-r--r-- 1 adminuser developers    0 Aug 15 19:41 developers.txt
-rw-r--r-- 1 adminuser sre    0 Aug 15 19:41 sre.txt
```

每个文件现在属于各自的组。这意味着只有拥有该文件的组成员才能拥有指定的权限；即读取权限(中间三个字符`r--`所示)。然而，该组之外的所有其他用户也具有读取权限(如最后三个字符`r--`所示)。

因此，考虑到我们希望将文件访问权限仅限于属于文件组的成员，我们来更改一下。

## 修改权限

要设置或更改权限，请使用`chmod`(*更改模式*的简称)命令(详见`man`页)。

使用此命令时，我们指定以下内容:

*   **修改谁的权限:** `u`为*用户(所有者)*`g`为**组*`o`为*所有其他用户*。*
*   ***如何更改权限:** `+`(加号)到*添加*权限，`—`(减号)到*删除*权限或`=`(等号)到*保持不变*。*
*   ***改变什么权限:** `r`为读，`w`为写，`x`为执行。*

*使用上面的信息，让我们取消组外用户的读取权限，并为组添加写入和执行权限，如下所示:*

```
*$ sudo chmod g+wx,o-r leads.txt
$ sudo chmod g+wx,o-r developers.txt
$ sudo chmod g+wx,o-r sre.txt*
```

*运行`ls -l`命令查看修改后的权限。输出应该是:*

```
*-rw-rwx--- 1 adminuser leads    0 Aug 15 19:42 leads.txt
-rw-rwx--- 1 adminuser developers    0 Aug 15 19:42 developers.txt
-rw-rwx--- 1 adminuser sre    0 Aug 15 19:42 sre.txt*
```

*要证明属于一个组的文件不能被另一个组访问，请以不同的用户身份登录；例如，`user5`(属于`developers`组)，使用以下命令:*

```
*$ sudo su user5*
```

*现在，让我们尝试读取一个不属于`developers`组的文件。运行以下命令查看`leads.txt`文件:*

```
*$ cat leads.txt*
```

*您应该会收到一条警告，上面写着:*

```
*cat: leads.txt: Permission denied*
```

*这是意料之中的，因为`user5`没有权限读取`leads.txt`文件的内容，因为该文件属于`leads`组，而不是`developers`组。*

## *结论*

*这篇小教程中分享的信息并没有穷尽 Linux 中管理用户、组和权限的主题。但是这应该让任何初学者，比如我自己，开始使用 Linux。*

***PS:我决定在学习 bash 脚本的同时找点乐子，写了脚本** `**create_users.sh**` **和** `**delete_users.sh**` **来自动创建和删除上面描述的用户。见此处**[](https://github.com/Z11mm/bash-scripts)****。*****

*****————————————感谢阅读！—————*****