# Docker 快速提示:CMD 和入口点

> 原文：<https://medium.com/codex/docker-quick-tip-cmd-entrypoint-c6ec3eabe688?source=collection_archive---------7----------------------->

![](img/112f4b53e61c2fb3b1b15674907ea224.png)

弗兰克·麦肯纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

首先，我们需要了解容器生命周期的基础。

容器的创建有一个简单的目的，那就是执行一项任务。当这个任务结束时，容器也被转移到“Exited”状态。

知道了这些，我们开始游戏吧。

在开始之前，如果你的本地环境中没有安装 docker，如果你想练习这篇文章中将要说的内容，你可以使用[来玩 Docker](https://labs.play-with-docker.com) 。

首先，让我们通过 docker run 命令运行一个 Alpine Linux 映像。

```
**docker run alpine**
```

之后，如果您运行 docker ps 命令，列出活动容器，在运行状态下，您会看到我们没有任何容器在运行。

```
**docker ps**CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

现在，如果您运行带有-a 选项的 docker ps 命令，您会注意到我们有一个处于“Exited”状态的 Alpine Linux 容器。

```
**docker ps -a**CONTAINER ID   IMAGE     COMMAND     CREATED         STATUS                     PORTS     NAMES441f5a414b40   alpine    "/bin/sh"   3 seconds ago   **Exited** (0) 2 seconds ago             naughty_elgamal
```

*注意:-a 选项显示所有容器，而不考虑状态，而 docker run 命令不带-a，只显示正在运行的容器。*

现在，让我们再次运行 run 命令，但是有所不同，使用了一些额外的指令。

```
**docker run alpine echo Hello**Hello
```

注意，在这种情况下，执行的输出是单词“Hello”。

这一次，如果您再次运行 ps -a docker，您将注意到一个小的变化，即 COMMAND 列的值。

```
**docker ps -a**CONTAINER ID   IMAGE     **COMMAND**        CREATED         STATUS                     PORTS     NAMESeffdd406f285   alpine    **"echo Hello" **  2 minutes ago   Exited (0) 2 minutes ago             keen_euclidb7e953228af4   alpine    **"/bin/sh"**      2 minutes ago   Exited (0) 2 minutes ago             awesome_chatterjee
```

为了理解这里发生了什么，让我们看两件事:

1)[docker 运行命令:](https://docs.docker.com/engine/reference/commandline/run/)

```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

注意，除了选项之外，RUN 命令还可以接收命令和 ARGS。知道了这一点，如果我们用指令“echo Hello”分析上面的命令的执行，在那之后，命令“docker ps -a”的执行的输出，我们注意到命令列的值的变化，我们看到容器执行的任务发生了变化。

*注意:在这个场景中，“回声”被解释为命令，“你好”被解释为 ARGS。*

但是如果我不输入 COMMAND 和(或)ARGS 会怎么样呢？

为了解释这一点，我们来看第二项。

2) [我们再来看阿尔卑斯图像 Dockerfile:](https://github.com/alpinelinux/docker-alpine/blob/6046c206b93945695d9c3efedcafe629a327fd85/x86_64/Dockerfile)

```
FROM scratch
ADD alpine-minirootfs-3.14.2-x86_64.tar.gz /
**CMD ["/bin/sh"]**
```

每个图像必须包含一个 CMD 或 Entrypoint，它表示容器的任务在其生命周期中应该执行什么。在 Alpine 的例子中，我们看到 CMD 是“/bin/sh”。但是，我们可以通过命令在“docker run”命令期间覆盖默认 CMD，就像我们在上面的“echo Hello”中所做的那样。

**但是 Entrypoint 适合在哪里，它和 CMD 有什么区别？**

为了解释这一点，让我们考虑下面的场景，假设我们希望有一个图像，它获得一个名称并返回短语“Hello+the name enter”，但是我们不想每次运行容器时都传递命令“echo”来从这个图像开始，我们只想将名称作为参数插入。

为了举例说明这一点，让我们从 Alpine 创建一个自定义图像来满足这一要求。

为了创建我们的图像，docker 文件应该是这样的:

```
FROM alpineENTRYPOINT ["echo", "Hello"]
```

准备好 docker 文件后，让我们来建立我们的形象。在 Dockerfile 文件所在的同一目录中，执行以下命令:

```
**docker build -t custom-alpine .**
```

现在，为了查看我们的图像是否创建正确，让我们运行“docker images”命令。

```
**docker images**REPOSITORY      TAG       IMAGE ID       CREATED      SIZEalpine          latest    14119a10abf4   4 days ago   5.6MB**custom-alpine**   latest    f92503ca5a3f   4 days ago   5.6MB
```

现在让我们从自定义图像运行一个容器，将一个名称作为参数。

```
**docker run custom-alpine Thiago**Hello Thiago
```

按照提议场景的要求，名称作为一个项目包含在 Entrypoint 数组中，因此容器打印出短语“Hello Thiago”。

因此，我们可以总结出 CMD 和 Entrypoint 之间的主要区别是，在 Entrypoint 的情况下，您可以保留默认命令，而不会在“docker run”命令的执行过程中被覆盖，而在 CMD 的情况下，该命令会被覆盖。

现在，如果我们想保留一个默认名称，以防在执行“docker run”命令时没有提供名称，该怎么办呢？
为此，我们可以将 Entrypoint 与 CMD 结合使用，我们的 docker 文件如下所示:

```
FROM alpineENTRYPOINT ["echo", "Hello"]CMD ["Tony Stark"]
```

随着 docker 文件如上所述的改变，让我们运行我们的映像的构建，现在生成它的版本 2。

```
**docker build -t custom-alpine:v2**
```

映像构建完成后，现在让我们从这个新映像运行一个容器，而不通知任何名称。

```
**docker run custom-alpine:v2**Hello Tony Stark
```

注意，现在，在没有提供名称的情况下，短语“Hello Tony Stark”作为容器执行的任务的输出来呈现。

如果我们运行一个新的容器，现在将一个名称作为参数，我们将得到以下输出:

```
**docker run custom-alpine:v2 Thiago**Hello Thiago
```

**快速提示**

对于像 Kubernetes 这样的容器编排器，在 YAML 的容器规范中，CMD 参数通过“args”属性替换，Entrypoint 通过“command”属性替换。

在上面的例子中，我们会有这样的情况:

```
apiVersion: v1
kind: Pod
metadata:
  name: custom-alpine-pod
spec:
  containers:
  - name: custom-alpine-container
    image: custom-alpine:v2
    **command: ["echo", "Hello"]**
    **args: ["Tony Stark"]**
```

*注意:这只是在 Kubernetes 中使用 CMD 和 Entrypoint 的一个例子。*

**结论**

好了，我希望我已经澄清了在 Docker 容器的生命周期中 CMD 和 Entrypoint 之间的区别，什么时候使用其中的一个，什么时候组合它们。