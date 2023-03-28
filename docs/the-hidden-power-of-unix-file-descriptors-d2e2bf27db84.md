# Unix 文件描述符隐藏的力量

> 原文：<https://medium.com/codex/the-hidden-power-of-unix-file-descriptors-d2e2bf27db84?source=collection_archive---------2----------------------->

![](img/712556a983a55bdc8bc2ce5534a193d9.png)

照片由[埃罗尔·艾哈迈德](https://unsplash.com/@erol?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 文件描述符

在 Unix 和类 Unix 操作系统中，**文件描述符**是文件的唯一标识符。例如，`/etc/environment`是引用环境变量的系统级配置文件的描述符。目前为止没什么特别的。

## 常规文件、符号链接、目录

但这还不是故事的全部。您可能知道，除了常规文件，文件描述符还可以引用其他文件类型，如符号链接和目录。

**符号链接**是指向文件的链接，其行为就像文件本身一样，但是你可以移动它们、复制它们和删除它们，而不会影响原始文件。如果您修改原始链接或符号链接的内容，两者会保持同步。例如，如果您想在不同用户之间共享文件或进行文件管理，这是非常方便的。例如，在我的机器上，我喜欢将我的工作项目放在`home/tdeneire/work`附近，但这实际上只是一个到`/home/tdeneire/backup/Dropbox/projects/work`的象征性链接(这是很难管理的)，这样整个目录就会自动备份到云中…

这个目录符号链接的例子展示了在 Unix 中，**目录**也是一种文件类型。许多对常规文件有效的操作(删除、复制、移动、重命名等)对目录也有效。同样，这是使用文件描述符完成的，例如主目录，在我的例子中是`/home/tdeneire`。

## 设备文件

POSIX 标准还定义了更多的文件类型，可以用文件描述符来处理。例如，所谓的 [Unix 域套接字](https://en.wikipedia.org/wiki/Unix_domain_socket)，它是一个数据通信端点，用于在内核中的进程间交换数据。另一个有趣的类别是**设备文件**，传统上安装在`/dev`中，它们是设备驱动程序的接口，出现在文件系统中，就好像它们是普通文件一样。在这种情况下，最有趣的可能是所谓的**伪设备文件**。这些并不对应物理硬件设备，而是为操作系统提供一定的工具。一个例子是`/dev/null`，它接受并丢弃写入其中的所有输入，如果您希望某个进程安静地运行，这非常有用:

```
my_command > /dev/null
```

另一个方便的工具是`/dev/random`，它公开了由内核所谓的[密码安全伪随机数发生器生成的随机字节流。](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator)以下命令要求来自`/dev/random`的八个字节以规范(`-C`)表示的十六进制转储:

```
hexdump -C -n 8 /dev/random*00000000  95 c1 86 a8 b8 7c 42 ee                 |.....|B.|
00000008*
```

## 命名管道或 FIFOs

最后一种 Unix 文件类型是**命名管道**，它不是传统的匿名管道，而是为管道创建一个文件描述符，这样两个独立的进程就可以通过管道的名称访问同一个管道。使用命令`mkfifo`创建一个命名管道。例如，您可以创建一个管道，并设置`gzip`来压缩通过管道传输给它的任何东西:

```
mkfifo my_pipe
gzip < my_pipe > out.gz &
```

`&`将这个进程从 shell 中分离出来，然后允许在一个单独的进程 shell 中独立地发送要压缩的数据:

```
cat file > my_pipe
```

之后，您可以像删除常规文件一样，用`rm my_pipe`删除命名管道。

当然，您可以通过使用一个临时文件来完成同样的任务，但是请记住，写一个临时文件既费时又会导致更多的 I/O 和硬盘上更少的可用空间。对于一些操作，比如将数据加载到数据库中(MySQL 和 PostgreSQL 都支持使用命名管道)，如果数据不需要先写入文件，就会有很大的不同…

# 一切都是一个文件

上述所有文件描述符的使用解释了为什么人们说在 Unix [**中一切都是文件**](https://en.wikipedia.org/wiki/Everything_is_a_file) 。事实上，正如维基百科所说，

> Unix 及其衍生产品的一个定义特性是，大量的输入/输出资源，如文档、目录、硬盘、调制解调器、键盘、打印机，甚至一些进程间和网络通信，都是通过文件系统名称空间公开的简单字节流。

## /proc

以上例子说明，说一切都是文件*描述符*更准确。即使有些东西看起来像文件，你可以把它们当作文件来处理，但它们不一定是文件。例如，前面提到的伪设备文件就是这种情况，而`/proc`下的纯虚拟文件系统将许多系统属性公开为文件。在这里您可以找到文件，或者更确切地说是文件描述符，比如`/proc/cpuinfo`、`/proc/partitions`、`/proc/uptime`和`/proc/version`，它们都是不言自明的。

`/proc`中另一个有趣的例子是`stdin`、`stdout`和`stderr`的文件描述符。你看，每个进程都有自己的文件描述符，你可以在`/proc/{PID}/fd`下面找到，后面是`0`代表`stdout`，`1`代表`stdin`和`2`代表`stderr`。例如，您可以启动一个进程，再次将其分离，以便向您显示其 PID:

```
sleep 30 && echo "hello" &*[1] 41985*
```

然后，您可以查看针对该流程向`stdout`写入了什么:

```
cat /proc/41985/fd/1
```

三十秒延迟结束后，您会看到`hello`出现。在这之后，你需要用`CTRL+C`终止这个进程，因为`cat`没有到达 EOF(文件结束)，所以它不知道什么时候停止。

尽管使用进程的特定 PID 可能很有趣(例如，您可以检查没有响应的进程)，但是您也可以省略 PID，使用通用的数字描述符`0`、`1`和`2`。例如，下面的命令将`stderr`重定向到`stdout`，这样您就可以捕获流程的输出和/或可能的错误:

```
cat this_file_does_not_exist > result 2>&1
cat result*cat: this_file_does_not_exist: No such file or directory*
```

## /sys

Unix 文件描述符的最后一个有趣的类别可以在`/sys`中找到。一些可能会让您感到惊讶的显著示例是电池状态、容量(%)，等等:

```
cat /sys/class/power_supply/BAT0/status*Discharging*cat /sys/class/power_supply/BAT0/capacity*46*
```

甚至是关于你的机器温度的信息！看看我在某个地方找到的这个漂亮的一行程序(虽然我想不起在哪里了):

```
paste <(cat /sys/class/thermal/thermal_zone*/type) <(cat /sys/class/thermal/thermal_zone*/temp) | column -s $'\t' -t | sed 's/\(.\)..$/.\1°C/'*acpitz           25.0°C
x86_pkg_temp     48.0°C
GEN1             38.0°C
INT3400 Thermal  20.0°C
SEN1             41.0°C
SEN2             35.0°C
SEN3             37.0°C
SEN4             40.0°C
TMEM             39.0°C
pch_skylake      45.5°C
B0D4             48.0°C*
```

我不确定最后这些例子是普通文件，还是像`/proc`一样，可以通过文件描述符访问的系统数据。但关键是，这根本不重要。通过文件描述符，Unix 系统以统一的方式通过文件系统公开数据，这意味着您可以在各种资源上使用相同的工具、实用程序和 API。

这就是 Unix 文件描述符真正隐藏的力量！

*嗨！*👋我是汤姆。我是一名软件工程师、技术作家和 IT 倦怠教练。如果想取得联系，可以查看[*https://tomdeneire . github . io*](https://tomdeneire.github.io/)