# 解释 PHP 的 Docker 模板

> 原文：<https://medium.com/codex/docker-template-for-php-explained-d674018e7cef?source=collection_archive---------0----------------------->

![](img/20c9b0df9bd369f9bfdbc2da70e4a0a1.png)

[滕玉红](https://unsplash.com/@live_for_photo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

几年前，当 [Docker](https://www.docker.com) 刚刚兴起，很少有人使用它，我们大多数人都使用[vagger](https://www.vagrantup.com)作为本地开发环境时，我开始在一家所有东西都在 Docker 中构建的公司工作。

我以前从未用过它。我非常习惯流浪，我想学一些我认为对我没有什么好处的新东西的愿望很少。我错了，今天我们都知道了。

但不管怎样，我在那里，我需要学习。所以我打开了我要做的第一个项目，并开始做通常的考古工作，以了解 docker 将如何帮助我运行这个项目。

在没有任何线索的两个小时后，我问了我的一所大学。随着时间的推移，我习惯了这种我以前从未使用过的新容器技术，但这可能花费了过多的时间…因为这个项目一点也不友好。

这些年来，我已经学会了如何用一种简单易用的方式来设置 Docker，而不需要知道每一个细节，这很有趣。今天我想给你一个简单易用的模板，让你在 10 分钟内明白 Docker 是如何工作的。

我们走吧！

# 文件结构

首先，我们需要了解文件在工作区文件夹中是如何分布的，更重要的是，我们应该把项目文件放在哪里。

有三个重要的文件夹: *app* 、 *bin* 和 *docker* 。

*   app:是你的项目文件应该放的地方。如果你使用的是 Symfony，Laravel，或者其他类似的框架，根文件夹的内容应该在 *app* 文件夹里面。
*   bin:我们有一些方便的文件来更快地运行 docker 命令。
*   docker:是我们设置 docker 的地方。稍后我会详细解释。

在 *app* 文件夹里，你还会发现两个:bin 和 public。这些文件夹模仿了框架用来分发控制台和服务器入口点的方式，您可以使用这些框架工具来改变这一点。我已经包括了两个*假的*文件，index.php 和 console.php，来测试每个选项。

# 配置 Docker

Docker 提供了一个 CLI 工具来单独构建和运行容器映像，一次一个。要知道， *docker build -t 标签。*或 *docker 运行标记。*

如果你想运行多个镜像，并且我们想同时运行 *php-fpm* 和 [*nginx*](https://nginx.org) ，那么最好的选择——在本地环境中——是[*docker——compose*](https://docs.docker.com/compose/)*。*有了 *docker-compose* 你就可以*计划*你需要的东西，然后运行它。这个*计划*建立在一个名为 *docker-compose.yaml* 的文件中。这是我们的 *docker-compose.yaml* 文件(docker/docker-compose.yaml):

让我们来分析文件:

*   version:是您想要使用的 docker-compose API 的版本。你可以在这里找到更多细节[。](https://docs.docker.com/compose/compose-file/compose-versioning/)
*   服务:在这里您将定义您需要的服务:
*   控制台:php-cli 的服务，用于运行控制台；你想要使用的 docker 的图像，以及你想要与容器共享的文件夹——更多信息请见下文。和工作目录(默认文件夹)。
*   PHP-fpm:PHP-fpm 的服务；你想要使用的 docker 的图像，以及你想要与容器共享的文件夹——更多信息请见下文。和工作目录(默认文件夹)。
*   nginx:nginx 的服务；你想要使用的 docker 的图像，以及你想要与容器共享的文件夹——更多信息请见下文。和工作目录(默认文件夹)。您还可以定义希望在主机和容器之间共享的端口——主机中的端口 80 指向容器中的端口 80。
*   共享文件夹:我们在所有服务中共享 *app* 文件夹——是的，nginx 也需要这个。我们共享 host.conf 文件来服务我们的 *app/public/index.php* 文件。您可以在下面看到主机文件:

由于这篇文章的意图不是解释 *nginx* 如何工作，我将只指出*fastcgi _ pass php-fpm:9000*部分:它只是使用服务 *php-fpm* 从 *nginx* 运行 PHP。

你可能想知道联网是如何根据我们定义的两个服务工作的: *php-fpm* 和 *nginx* 。

答案其实很简单:如果你不定义网络，docker 会创建一个默认的网络，所有的服务都在里面，并且彼此可见。这正是我们所需要的。

所以现在我们已经定义了我们想要的服务以及它们是如何构建的——我们使用来自 [docker hub](https://hub.docker.com) 的 docker 映像。现在，我们如何启动这些服务来运行控制台呢？

# 运行控制台

要运行控制台，我们应该做一些类似于*docker-compose run what params*等的事情。但是，每次我们想要运行控制台时都键入这个代码，这与生产效率是相反的，所以我创建了一个简单的 bash 文件:

第 1 行和第 3 行只是获取保存脚本的文件夹。当您想从不同于根目录的地方运行脚本时，这很方便。

第 6 行是运行 docker-compose 的实际行。

我们提供了到 *docker-compose.yaml* 文件的路径，然后是*运行*，然后是我们想要运行的服务，在我们的例子中是*控制台*，然后是实际的命令，也就是*PHP console.php 参数*。

所以不能只跑*。/bin/console.php params* 。如果你这样做，docker 将从 docker hub 中提取 php-cli 映像，然后执行 *app/bin/console.php* 输出*这是控制台*，这是我们构建的假控制台。

# 为应用服务

如果我们想服务 nginx 和 php-fpm 怎么办？我们还有另一个方便的 bash 文件:

第 5 行和第 6 行完成了这个任务。

首先，我们提取图像。然后，再一次，我们提供到 *docker-compose.yaml* 文件的路径，然后 up-d-d param 分离进程-，然后是我们想要运行的服务， *php-fpm* 和 *nginx。*

我们只需 *bin/up.sh* 来完成这个任务。

图像被拉出来，运行，我们可以去 *localhost* 看 *Hello World！这也是我们伪造的。*

如果你想停止这些服务，就执行 *bin/stop.sh.* 你可以在这里看到 stop 脚本，我想我不需要解释它，因为它很简单:

# 查看日志

如果您想查看日志，您可以只 *docker logs -f container* ，其中-f 对日志进行跟踪-要查找容器 id，您可以只 *docker ps* ，它将输出容器列表:是第一列。

但是，日志在这种设置中是如何工作的呢？

嗯，基本上你输出到 stderr 和 stdout 的所有内容都会记录到日志中。就是这样。

# 在哪里可以找到模板

我已经将我的模板推送到 Github 中的一个公共回购中。点击[此处](https://github.com/stratdes/docker-for-php-template)分叉/下载。

# 最后的想法

基础设施可能很艰难。构建易于使用的框架和工具非常重要，这样开发人员可以从零开始高效工作。

例如，有了这个模板，任何开发人员都可以通过运行两个不同的命令——控制台和服务器——来运行项目，甚至不知道后面的事情是如何工作的。然后，有了足够的时间和信心，学习细节就更容易了。

如果你觉得这很有趣，你可以关注我的[媒体](/@stratdes)。请不要犹豫，分享你想要的模板和这篇文章的链接。