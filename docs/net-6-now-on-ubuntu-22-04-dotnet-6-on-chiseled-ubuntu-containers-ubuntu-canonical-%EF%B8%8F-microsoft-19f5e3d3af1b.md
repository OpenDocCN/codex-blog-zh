# 。NET 6 现在上 Ubuntu 22.04！凿过的 Ubuntu 容器上的 dotnet 6！Ubuntu Canonical ❤️微软

> 原文：<https://medium.com/codex/net-6-now-on-ubuntu-22-04-dotnet-6-on-chiseled-ubuntu-containers-ubuntu-canonical-%EF%B8%8F-microsoft-19f5e3d3af1b?source=collection_archive---------0----------------------->

![](img/74249014e0d0bc5ecd61bc4afd16b789.png)

[。NET 6](https://devblogs.microsoft.com/dotnet/announcing-net-6/) 现在[包含在 Ubuntu 22.04(海哲明)](https://ubuntu.com/blog/install-dotnet-on-ubuntu)中，只需`apt install dotnet6`即可安装。这个变化对于 Ubuntu 用户来说是一个重大的改进和简化。我们还发布了。这是 Canonical 提供的一种新的小型安全容器。这些改进是 Canonical 和微软新合作的结果。(原文可在[这里](https://devblogs.microsoft.com/dotnet/dotnet-6-is-now-in-ubuntu-2204/)找到)

下面是安装[的命令。Ubuntu 22.04 上的 NET 6 SDK](https://packages.ubuntu.com/jammy-updates/dotnet6):

```
sudo apt update
sudo apt install dotnet6
```

我们还宣布[。NET 6 有凿过的 Ubuntu 容器](https://github.com/ubuntu-rocks/dotnet)。我们在 [Canonical](https://canonical.com/) 的朋友开发了一种新的[凿子](https://github.com/canonical/chisel)方法来制作超小型容器图像。我们对此非常兴奋。凿出来的 Ubuntu 图片比你现在一直用的 Ubuntu 图片小`100MB`！

下面是拉新 ASP.NET 轮廓分明的图像的命令:

```
docker pull mcr.microsoft.com/dotnet/nightly/aspnet:6.0-jammy-chiseled
```

我们还更新了我们的 [dotnetapp](https://github.com/dotnet/dotnet-docker/blob/main/samples/dotnetapp/Dockerfile.chiseled) 和 [aspnetapp](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/Dockerfile.chiseled) 示例，以便您可以试用。带有凿过的 Ubuntu 容器的 NET。

这些新的容器映像显著改善了安全状况:

*   超小图像(缩小尺寸和攻击面)
*   没有包管理器(避免了一整类攻击)
*   无外壳(避免一整类攻击)
*   非根(避免一整类攻击)

最重要的是，Canonical 和微软致力于合作，以确保新的。新的 Ubuntu 版本提供了. NET 版本，并且它们可以很好地协同工作。这包括容器图像的安全更新和安全交付。

我们真的很兴奋。NET 6 在 Ubuntu 22.04 中可用，Canonical 选择与我们合作，作为他们推出凿刻 Ubuntu 图像的合作伙伴。这是 Canonical 对这个项目的评价。

> *“Ubuntu 现在有一个从开发到生产的端到端故事，支持超小型容器映像，从。NET 平台”，Canonical 的产品经理 Valentin Viennot 说。“我们认为这对我们两个社区都是一个巨大的进步；与合作。微软的 NET 团队使我们能够超越自我”。*

# Canonical 和微软

几个月前，Canonical 和微软的人开始合作，目标是让 Ubuntu 成为一个更好的环境。NET devs。

我们心中有两个主要目标:

*   简化使用。Ubuntu 上的 NET。
*   缩短 Canonical 和微软之间的供应链。

我们已经知道很多年了。NET devs 使用 Ubuntu。在我们交谈之后，很明显我们可以做一些事情来改善这种体验。让我告诉你我们送了什么。

# 。APT 中的 NET

现在可以安装[了。NET 6 带 APT](https://ubuntu.com/blog/install-dotnet-on-ubuntu) ，由 Canonical 通过 [source-build](https://github.com/dotnet/source-build) 构建。这些软件包在 Ubuntu 22.04(海哲明)和更高版本中可用。是升级到[海哲明](https://ubuntu.com/blog/ubuntu-22-04-lts-released)的一大理由！

> *注:请查看本* [*关于在 Ubuntu 22.04*](https://github.com/dotnet/core/issues/7699) *上使用* `[*packages.microsoft.com*](https://github.com/dotnet/core/issues/7699)` [*的咨询既然。NET 6 包含在 Ubuntu 中。*](https://github.com/dotnet/core/issues/7699)

有[多个包](https://packages.ubuntu.com/search?suite=default&section=all&arch=any&keywords=dotnet&searchon=names):

*   `[dotnet6](https://packages.ubuntu.com/jammy-updates/dotnet6)` —的。NET 6 SDK(简称)。
*   `[dotnet-sdk-6.0](https://packages.ubuntu.com/jammy-updates/dotnet-sdk-6.0)`——同上(长名)。
*   `[aspnet-runtime-6.0](https://packages.ubuntu.com/jammy-updates/aspnetcore-runtime-6.0)`—ASP.NET 核心
*   `[dotnet-runtime-6.0](https://packages.ubuntu.com/jammy-updates/dotnet-runtime-6.0)`——。NET 运行时

我将向您展示如何使用 Docker 安装这些映像(同样的模式适用于其他地方):

```
rich@kamloops:~$ docker run --rm -it ubuntu:jammy
root@7d4dfca0ef55:/# apt update && apt install -y dotnet6
root@7d4dfca0ef55:/# dotnet --version
6.0.108
```

如果这不起作用，您需要在/etc/apt/sources.list 中注册以下源:

```
deb [http://archive.ubuntu.com/ubuntu/](http://archive.ubuntu.com/ubuntu/) jammy-updates universe
```

Canonical 和微软将合作确保这些软件包每月更新一次。NET 团队发布时间表。这包括微软在公开发布之前与 Canonical 共享 [CVE 信息](https://github.com/dotnet/core/blob/main/release-notes/6.0/cve.md)(描述和代码)。类似地，Canonical 将在另一个方向共享安全信息。

注意事项:

*   我们目前缺少 Arm64 版本。这些很快就会到来。两家公司都是 Arm64 的大力支持者。
*   。NET 7 版本目前还不可用，可能要到 2010 年才会推出。净 7 GA。
*   [。NET SDK 工作负载](https://github.com/dotnet/designs/blob/main/accepted/2020/workloads/workloads.md)在包中不可用(对于任何 Linux 发行版)。此外。Linux 不支持. NET MAUI 工作负载。

# 。雕刻的 Ubuntu 容器中的网

你现在可以使用[。凿刻的 Ubuntu 容器中的 NET](https://github.com/ubuntu-rocks/dotnet)。凿开提供了最小的容器足迹，同时仍然是你所知道和信任的 Ubuntu。它类似于传统的[分发器](https://hackernoon.com/distroless-containers-hype-or-true-value-2rfl3wat)，带有为切片`.deb`包装定制的工具。

这些图片`100MB`比我们之前提供的 Ubuntu 图片要小，而且不包括根用户！

我们为 Arm64 和 x64 提供三层轮廓分明的 Ubuntu 容器图像。净 6 和 7:

*   `mcr.microsoft.com/dotnet/nightly/runtime-deps:6.0-jammy-chiseled`
*   `mcr.microsoft.com/dotnet/nightly/runtime:6.0-jammy-chiseled`
*   `mcr.microsoft.com/dotnet/nightly/aspnet:6.0-jammy-chiseled`

注意:图片将在我们的`nightly`回购中提供，而凿刻的产品在预览中。当它们在生产中得到支持时，我们将再次发布公告。这将是今年的某个时候，但我们还没有选择一个时间框架，因为我们一直专注于基本的支持。

Canonical 也在为。NET，其中包括新的 APT 包，通过 Docker Hub:

*   [https://hub.docker.com/r/ubuntu/dotnet-deps](https://hub.docker.com/r/ubuntu/dotnet-deps)
*   [https://hub.docker.com/r/ubuntu/dotnet-runtime](https://hub.docker.com/r/ubuntu/dotnet-runtime)
*   [https://hub.docker.com/r/ubuntu/dotnet-aspnet](https://hub.docker.com/r/ubuntu/dotnet-aspnet)

让我们来看看大小赢。以下所有大小都是未压缩的(在磁盘上，而不是注册表/线大小)。

首先是`runtime-deps`层。

*   Ubuntu 22.04(海哲明):`112MB`
*   凿刻的 Ubuntu 22.04(海哲明):`12.9MB`

而在光谱的另一端，`aspnet`层。

*   Ubuntu 22.04(海哲明):`213MB`
*   凿过的 Ubuntu 22.04(海哲明):`104MB`

这是一个真正惊人的差异！Canonical 的人已经想出了如何从这些图像中删除 100MB 的二进制文件和其他内容。当我们第一次开始谈论时，我们不知道我们会谈论这么大的差异！

细心的读者会注意到，轮廓分明的`aspnet`比现有的`runtime-deps`层要小。这太好了。

问[高山](https://alpinelinux.org/)长什么样是有道理的。这是一个较新的发行版，从一开始就被设计得非常小并且组件化。阿尔卑斯是`9.84MB`对`runtime-deps:6.0-alpine`，`100MB`对`aspnet:6.0-alpine`。这些都是令人印象深刻的数字，还是未经压缩的。这就是 Alpine 如此受欢迎的关键原因(也是我们出版的原因。多年来的网络图像)。

Alpine 很棒(我们也是那些人的[朋友)，但它并不适合每个人和每个应用程序，因为它使用](https://pkgs.alpinelinux.org/packages?name=dotnet*) [musl](http://musl.libc.org/) ，这是一个不同的(不兼容的)`libc`变体。只有当你的应用包含本地库时，这才是重要的。如果没有(而且大部分。NET apps 没有)，你不需要担心这个细节。的。NET 产品本身可以很好地运行`musl`或`[glibc](https://www.gnu.org/software/libc/)`，并且在 [dotnet/runtime](https://github.com/dotnet/runtime) 测试中也能很好地运行。

客观地看，如果你使用 Ubuntu 进行开发，并且一直希望有一个小的 Ubuntu 投入生产，这确实是一个好消息。现在，您有了一条从 dev box 到 cloud 的直接路径，没有任何发行版兼容性问题。看到 Ubuntu 和 Alpine 在同一个球场上，真是令人惊讶。在伟大的工程成就上向典范的人们致敬。

同样值得一提的是 [Chainguard](https://www.chainguard.dev/) 正在研究[最小容器映像，以实现安全的未来](https://blog.chainguard.dev/minimal-container-images-towards-a-more-secure-future/)。那个项目是在 GitHub 组织下运行的。我们正在关注这个项目，很高兴看到人们对更小、更安全的容器图像越来越感兴趣。我们相信极小+非根容器映像是未来的趋势。

就像我们的[阿尔卑斯山图片](https://github.com/dotnet/dotnet-docker/blob/1297d21bbf695bcb87580bea2ccefdced894eeeb/src/runtime-deps/3.1/alpine3.16/amd64/Dockerfile#L19-L20)，我们选择不包括 [ICU](https://icu.unicode.org/) 。它可能会使图像的大小加倍。这意味着我们已经启用了[全球化不变模式](https://github.com/dotnet/runtime/blob/main/docs/design/features/globalization-invariant-mode.md)。对于一些应用程序来说，这很好，而且大小很重要。对其他人来说，这是一个交易破坏者。我们可能需要根据反馈调整计划的这一部分。我们已经[记录了将 ICU 添加到您的图像中的模式](https://github.com/ubuntu-rocks/dotnet/issues/21)。

让我演示一下这些图片，让大家明白这些图片是如何(有意)受到限制的。

```
% docker run --rm mcr.microsoft.com/dotnet/nightly/runtime-deps:6.0-jammy-chiseled-amd64
docker: Error response from daemon: No command specified.
See 'docker run --help'.
```

让我们再试一次。

```
% docker run --rm mcr.microsoft.com/dotnet/nightly/runtime-deps:6.0-jammy-chiseled-amd64 bash
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "bash": executable file not found in $PATH: unknown.
```

啊？怎么了？它们不起作用！这才是重点。这些是类似设备的容器图像。他们被减到最少。他们只打算做你设计他们做的事情。这是让他们更安全的方面。如果这种体验不舒服，你可以一直使用常规的 Ubuntu 图片。我们会继续提供。他们不会离开。

我们不会提供一个轮廓分明的 SDK 映像。没有明显的强烈需求。事实上，一个轮廓分明的 SDK 映像可能很难用于某些场景。可以继续使用现有的海哲明 SDK 镜像:`mcr.microsoft.com/dotnet/sdk:6.0-jammy`。如果需要一个轮廓分明的 SDK 映像，我们很乐意重新考虑。

# 使用轮廓分明的容器图像

对于大多数应用程序来说，在使用这些新的容器图像时，就你的`Dockerfile`看起来像什么而言，不会有任何显著的不同。

我们对样品进行了更新，以使用这些新的容器图像:

*   [点 netapp](https://github.com/dotnet/dotnet-docker/blob/main/samples/dotnetapp/Dockerfile.chiseled)
*   [aspnetapp](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/Dockerfile.chiseled)

我将向您展示使用 [dotnetapp](https://github.com/dotnet/dotnet-docker/blob/main/samples/dotnetapp/Dockerfile.chiseled) 有多简单。

Dockerfile 文件几乎没有什么不同。

```
FROM mcr.microsoft.com/dotnet/sdk:7.0-jammy AS build
WORKDIR /source# copy csproj and restore as distinct layers
COPY *.csproj .
RUN dotnet restore --use-current-runtime# copy and publish app and libraries
COPY . .
RUN dotnet publish -c Release -o /app --use-current-runtime --self-contained false --no-restore# final stage/image
FROM mcr.microsoft.com/dotnet/nightly/runtime:7.0-jammy-chiseled
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "dotnetapp.dll"]
```

只有最后的`FROM`语句与我们的标准 [Ubuntu Dockerfile](https://github.com/dotnet/dotnet-docker/blob/main/samples/dotnetapp/Dockerfile.ubuntu-x64) 真正不同。

我现在将构建示例:

```
rich@MacBook-Air-2 dotnetapp % pwd
/Users/rich/git/dotnet-docker/samples/dotnetapp
rich@MacBook-Air-2 dotnetapp % docker build -t dotnetapp-chiseled -f Dockerfile.chiseled .
rich@MacBook-Air-2 dotnetapp % docker images | grep dotnetapp-chiseled
dotnetapp-chiseled                 latest      bf7e125bd182   20 seconds ago   90.5MB
```

注:我一个都没用。净修整特征。当然，这个图像可以做得更小。

让我们启动容器:

```
rich@MacBook-Air-2 dotnetapp % docker run --rm dotnetapp-chiseled
         42
         42              ,d                             ,d
         42              42                             42
 ,adPPYb,42  ,adPPYba, MM42MMM 8b,dPPYba,   ,adPPYba, MM42MMM
a8"    `Y42 a8"     "8a  42    42P'   `"8a a8P_____42   42
8b       42 8b       d8  42    42       42 8PP"""""""   42
"8a,   ,d42 "8a,   ,a8"  42,   42       42 "8b,   ,aa   42,
 `"8bbdP"Y8  `"YbbdP"'   "Y428 42       42  `"Ybbd8"'   "Y428.NET 7.0.0-preview.7.22375.6
Linux 5.10.104-linuxkit #1 SMP PREEMPT Thu Mar 17 17:05:54 UTC 2022OSArchitecture: Arm64
ProcessorCount: 4
TotalAvailableMemoryBytes: 3.83 GiB
```

然后，让我们试着闯入:

```
rich@MacBook-Air-2 dotnetapp % docker run --rm --entrypoint bash dotnetapp-chiseled
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "bash": executable file not found in $PATH: unknown.
rich@MacBook-Air-2 dotnetapp % docker run --rm --entrypoint apt  dotnetapp-chiseled install -y bash curl
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "apt": executable file not found in $PATH: unknown.
```

我的“红队”技能让我失望了。注意`docker exec`会有相同的结果。

现在我将更详细地描述凿刻的图像，因为你已经看到了它们的作用。

# 雕琢的 Ubuntu 容器

轮廓分明的 Ubuntu 容器是 distroless 概念的典范，最初由 Google 推广。在最初的实现中，一个发行版被剥离，只安装必要的软件包。凿开向前迈进了一步，它只在每个包中安装必要的目录和文件。

最初实现的另一个挑战是它不一定得到任何一方的支持。凿过的 Ubuntu 容器是一流的规范可交付产品。这意味着您可以使用超小型容器映像，并作为标准客户获得支持。

向谷歌致敬，它让我们开始走上这条道路。

如前所述，这种方法有很多价值:

*   超小图像(缩小尺寸和攻击面)
*   没有包管理器(避免了一整类攻击)
*   无外壳(避免一整类攻击)

凿刻的 Ubuntu 容器目前在预览中。当它们在生产中稳定并受支持时，我们将另行发布。

# 非根图像

我们已经[配置了所有新的。NET 凿开 Ubuntu 容器](https://github.com/dotnet/dotnet-docker/blob/fd39ea3eece8c8653348a717446ac41e93633784/src/runtime-deps/6.0/jammy-chiseled/arm64v8/Dockerfile#L13-L26)与一个[非根](https://stackoverflow.com/questions/59840450/rootless-docker-image)用户。这些图像不包括`root`用户，也不包括像`sudo`或`su`这样的根提升命令。这意味着不可能运用需要`root`的能力和行动。

非根映像是除了移除外壳之外的额外安全缓解措施(如`bash`)。非根映像在逻辑上是独立的，并作为无根运行[守护进程的补充。特权的每一次](https://docs.docker.com/engine/security/rootless/)[减少都会帮助](https://seclists.org/oss-sec/2019/q1/119)。

如果您需要访问特权资源，您可以在您的`Dockerfile`中添加`root`用户。您可以这样做，但这是您要做出的具体安全决定。

凿刻的图像是器具状的，不是通用的。我们觉得他们为我们提供了一个最终交付非根映像的机会。这是我们未来政策的基础。类似设备的映像将作为非根映像交付，通用映像将根据基础映像的策略交付(可能由`root`用户配置)。然而，Canonical 的这个项目启发我们寻找一个折中的选择，即[提供不支持 root 的映像](https://github.com/dotnet/designs/pull/271)。

# 安全供应链

Canonical 已经有了安全的流程，可以直接将 Ubuntu 虚拟机映像交付给 Azure 供客户使用。我们想到 Canonical 可以对我们用来构建基于 Ubuntu 的 Ubuntu 容器基础映像做同样的事情。网络图像(规则和轮廓分明)。这就是我们现在使用的，而不是从 Docker Hub 拉。我们现在拥有一个有效的零距离供应链，所有规范资产都有已知的保管/出处。

我们正在做一些类似的事情，分享 [CVE](https://www.cve.org/) 修复。我们有一个共享的私有[虚拟单声道回购](https://github.com/dotnet/source-build/issues/2956)用于共享每月补丁。也是分享给红帽的。这意味着我们可以共同努力，以协调的方式在正确的时间进行正确的修复。

。NET 容器映像还没有被签署，但是它很快就会被签署。我们一直在努力提高我们以安全为中心的能力。

# 支持

Canonical 和微软一直在合作，给你更好的体验。这包括支持。您可以在熟悉的中报告问题。NET repos 像 [dotnet/core](https://github.com/dotnet/core) 和 [dotnet/runtime](https://github.com/dotnet/runtime) 。如果你想要商业支持，你应该从规范支持开始。Canonical 是支持 Ubuntu 包的最佳位置。如果需要，Canonical 可能会联系 Microsoft 以帮助解决问题。

安全研究人员发现规范提供的漏洞。NET 包仍然符合微软的标准。净赏金计划。

微软继续保持。NET 包在 Ubuntu 的[packages.microsoft.com](https://docs.microsoft.com/dotnet/core/install/linux)feed 中，我们打算继续前进。对于大多数用户，我们建议使用 Ubuntu 海哲明+附带的`dotnet6`包。这就是我要做的。这也是我们对红帽用户的指导。

> *注:请查看本* [*关于在 Ubuntu 22.04*](https://github.com/dotnet/core/issues/7699) *上使用* `[*packages.microsoft.com*](https://github.com/dotnet/core/issues/7699)` [*的咨询既然。NET 6 包含在 Ubuntu 中。*](https://github.com/dotnet/core/issues/7699)

继续使用微软软件包有两个主要原因:

*   你特别想要。NET 构建来自微软，而不是任何其他供应商。
*   微软软件包目标稍后。NET SDK 特性带(像`6.0.4xx`)而源代码构建跟踪`6.0.1xx`。这对 Windows 用户来说更相关，但对一些 Linux 用户来说可能更重要。

新的软件包可用于。NET 6+和 Ubuntu 22.04+。以前的。不支持. NET 和 Ubuntu 版本(新的软件包)。你必须使用现有的`packages.microsoft.com`饲料来使用。NET 在更早的 Ubuntu 版本上。分开，早些时候。Ubuntu 22.04 不支持. NET 版本，因为它们不支持 OpenSSL v3。

# 下一步是什么？

我们已经确定了一些[机会，使 Canonical 更容易消费。网源](https://github.com/dotnet/source-build/issues/2911)。我们将把重点放在眼前。这些改进也将使其他构建和发布的用户受益。来源于网络。

我们最近为. NET 建立了一个发行维护组，Canonical 是该组的成员之一。我们已经开始在那个论坛上讨论[潜在的源代码构建改进](https://github.com/dotnet/source-build/issues/2911)。其他发行版(构建。来源于. NET)欢迎加入。联系 dotnet@microsoft.com 了解更多信息。

Canonical 开始支持 x64，并将很快增加。Arm64 的网络封装。这是业界激动人心的时刻，有多种主线芯片架构需要支持。Ubuntu 和。NET 都有支持多种架构的悠久历史。

# 关闭

。NET 已经开放源代码 5 年多了。在我们 GitHub 项目的早期，我们觉得与 Canonical 的合作难以维系。我们已经学习了很多关于如何构建一个 OSS 项目，使它成为一个包含在 Linux 发行版中的候选者。这要感谢我们的[其他伙伴](https://docs.microsoft.com/dotnet/core/install/linux#official-package-archives)，他们教会了我们很多，尤其是 [Fedora](https://packages.fedoraproject.org/search?query=dotnet) 和 [Red Hat](https://access.redhat.com/documentation/en-us/net/6.0) 。回过头来看，很容易发现开源、信任和行业关系现在比我们开始时更加重要。我们很兴奋也很荣幸能与 Canonical 合作。

# StartupHakk:

无论是做一些专门的业务流程。我们专门让这些专家进入他们的领域，教他们如何开发，教他们成为全栈开发人员。你将会得到的是，你将会得到一个既能成为全栈开发者，又能成为其领域专家的人，我们认为这最终将会是， 最好的混合体之一，真正成为企业界的最佳利益所在，他们可以让这些专业人士成为开发人员，这最终将帮助他们在他们的领域变得非常优秀，我认为这是我们将真正努力的专业之一，所以请确保您了解 startuppack.com。

这是一个很好的机会，我们刚刚开始启动我们的编码训练营，这样你就可以找到你所在领域的专家，教他们成为一名开发人员，然后开始构建和学习你所获得的所有这些最重要的技能。所以一定要去看看 startuphack.com 的。