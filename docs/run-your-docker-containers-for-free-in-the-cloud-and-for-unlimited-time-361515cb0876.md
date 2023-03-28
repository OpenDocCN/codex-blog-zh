# 在云中无限期免费运行 Docker 容器

> 原文：<https://medium.com/codex/run-your-docker-containers-for-free-in-the-cloud-and-for-unlimited-time-361515cb0876?source=collection_archive---------0----------------------->

![](img/21c93fab597ee6b71faf2e697803022c.png)

拥有一个除了自己的机器之外的地方来运行 docker 容器会很方便。它有助于您加快个人项目的开发或减轻机器的繁重负担。

最近有人向我介绍了 [Oracle cloud](https://www.oracle.com/cloud/) ，以及它的无限期免费层。检查支持的[永远免费服务](https://www.oracle.com/cloud/free/#always-free):

*   2 个基于 AMD 的计算虚拟机，每个具有 1/8 OCPU**和 1 GB 内存
*   4 个基于 Arm 的 Ampere A1 内核和 24 GB 内存可用作一个虚拟机或最多 4 个虚拟机
*   2 个数据块卷存储，总共 200 GB
*   10 GB 对象存储—标准
*   10 GB 对象存储—不常访问
*   10 GB 归档存储
*   资源管理器:托管地形
*   5 OCI 堡垒

如您所见，他们对免费层非常慷慨，我们可以利用这一点做很多事情。

## 创建您的远程 Docker 服务

1.  创建一个 Oracle 云基础设施帐户(只需点击[此链接](https://bit.ly/free-oci-dat-k8s-on-arm))。
2.  [安装地形](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/oci-get-started)。
3.  安装 OCI 命令行界面。
4.  配置 [OCI 凭证](https://learn.hashicorp.com/tutorials/terraform/oci-build?in=terraform/oci-get-started)。

现在您可能已经猜到了，我们将使用 terraform 来提供 Docker 服务。基本上，terraform 将创建一个虚拟机实例，让它尽可能大，让它准备好，这样我们就可以 ssh 到机器中，并使用 docker 启动我们的容器。

5.使用 terraform 配置签出 [remote-docker](https://github.com/rafaelzimmermann/remote-docker) 项目，并输入其目录。

6.运行`terraform init`和`terraform apply`

如果您得到这样的错误:

`timeout -- last error: dial tcp IP_ADDRESS:22: connect: connection refused`

尝试将 terraform 生成的 id_rsa 添加到您的 ssh-agent ( `ssh-add id_rsa`)中，并再次运行`terraform apply`，id_rsa 应该被本地化到项目的根目录中。

如果您能够达到这一点，那么您现在已经有了一个虚拟机，您可以随心所欲地运行它(请查看 Oracle 关于此服务的条款和条件)。在流程的最后，terraform 被配置为显示新创建的实例的公共 IP。

我喜欢做的是将这个添加到`/etc/hosts`中，这样我就不需要通过 IP 地址调用它了。

使用 ssh 连接到远程服务器，并运行`docker ps`来检查 docker 是否按预期运行。

## 部署您的容器

好了，现在你想从你的本地机器开始运行容器，但是出于某种原因你也在本地运行一些容器。因此，我们需要以一种容易在两种环境之间切换的方式来做这件事。

Docker 有一个嵌入在其客户端的工具，用于检查您可以运行的上下文:

`docker context ls`

让我们为远程 docker 服务创建一个上下文:

`docker context create remote --docker "host=ssh://docker@IP_PROVIDED_BY_TERRAFORM`

现在，如果您再次列出您的上下文，您应该会看到您新创建的上下文。下一步是在上下文之间切换:

`docker context use remote`

现在，您可以运行通常在本地执行的任何命令。要检查它是否工作，请尝试部署一个 nginx 容器。

`docker run -d -p 8080:80 nginx`

在这之后，你应该能够在你的浏览器上打开:`[http://IP_PROVIDED_BY_TERRAFORM](http://IP_PROVIDED_BY_TERRAFORM):8080`并且你应该看到 docker 的默认 index.html。

## 结论

这个项目无耻地基于[jérme Petazzoni 项目](https://github.com/jpetazzo/ampernetacle)，其中他描述了如何使用免费层在 Oracle 基础设施上构建 Kubernetes 集群。他的项目非常有助于理解地形和库伯内特的构造。我强烈推荐你去看看。

我从他的项目中衍生出这个产品，因为学习如何部署 K8S 集群是很棒的，但是把它放在身边对我来说并没有多大用处。Docker 服务在你开发的时候有它更有用。

我知道这有什么帮助，特别是当你的机器达到极限的时候

## 来源

*   [https://www.oracle.com/cloud/free/#always-free](https://www.oracle.com/cloud/free/#always-free)
*   创建自己的 Kubernetes 集群:[https://github.com/jpetazzo/ampernetacle](https://github.com/jpetazzo/ampernetacle)