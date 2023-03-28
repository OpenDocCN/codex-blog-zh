# 如何使用 Ansible 安装 Docker

> 原文：<https://medium.com/codex/how-to-install-docker-using-ansible-84d40005169?source=collection_archive---------7----------------------->

![](img/7e554b7b1a02cbb19c74a6e622b6b8ea.png)

由[潘卡杰·帕特尔](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

随着 Docker 和容器化的出现，像 Ansible、Puppet 或 Chef 这样的工具越来越少，因为系统的大部分配置都发生在容器中。

此外，由于像 Google Cloud 或 AWS 或 Azure 这样的云计算平台正在提供托管的 Kubernetes 集群，配置机器的必要性每天都在降低。

但是，如果你买不起云服务，只想买一个 VPS 或一台专用机器，安装 Docker 和 Docker Compose 来运行几个容器，会发生什么呢？

你应该用手做吗？

一点也不。有责任营救。

在本文中，我将向您解释如何安装、配置和使用 Ansible 来安装 Docker。

# 如何安装 Ansible

安装 Ansible 包括安装一些 CLI 工具，这非常容易，不管您使用的是什么平台。我会教你如何在 Mac 和 Ubuntu 中安装 Ansible。

对于 Mac 用户:您可以使用 Homebrew 安装 Ansible，只需运行以下命令:

```
brew install ansible
```

对于 Ubuntu 用户:你可以运行以下命令安装 Ansible:

```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

你可以在官方文档中找到更多信息。

# 如何配置 Ansible

配置 Ansible 是一个非常简单的操作。

首先，您需要创建一个名为剧本的目录。这是您将存储 YAML 文件和配置您的远程主机所需的步骤的地方——您希望安装 Docker 和 Docker Compose(使用 Ansible)的 VPS。

接下来，您需要创建一个名为 inventory 的文件——实际上可以叫它什么都行，它包含以下内容:

VPS 的 IP 地址

仅此而已。很简单。唯一需要考虑的是，您需要能够使用 SSH 密钥对这台机器进行 SSH。因此，如果`ssh user@IP_OF_THE_VPS`已经为你工作，你就准备好执行可行的行动手册。

# 一本安装 Docker 和 Docker Compose 的剧本

这是 YAML 剧本的全部内容，我会一步一步解释:

首先，hosts 键的值是 all，这意味着将在所有可用的清单主机上执行剧本。因为我们只有一个，我们可以设置所有的东西，一切都会好的。

然后，我们有 remote_user 键:这是我们用来 SSH 到机器的用户，比如说 ubuntu，但是它也可以是拥有 SSH 访问权限和适当权限的任何用户。

成为:这意味着我们将使用 sudo 执行不同的命令。这是安装软件包、更改权限、组等所需要的。如果你打开 Docker 官方文档，你会发现所有的命令都是以 sudo 运行的。

接下来，您会发现一组任务，其中包含我们将在远程主机上运行的不同进程。

任何任务都有一个名称、一个类似动作的 apt、service 或 ansible.builtin.group，还可以选择一个循环。动作使用参数，比如 apt 中的名称或状态。

第一个任务叫做*安装依赖项*，安装以下软件包:

*   apt-transport-https
*   ca 证书
*   卷曲
*   gnupg 代理
*   软件-属性-通用

您可以在 Docker 的文档中查看这些依赖项是安装 Docker 所必需的。

如果您查看任务，您将看到状态具有值 present。这意味着 Ansible 将确保这些包存在于机器中，所以它将只在需要时安装——这就是 Ansible 是幂等的原因。

下一个任务，*添加 GPG 密钥*，向系统添加一个 APT 密钥。如果你熟悉 Ubuntu，你就会知道这是安装某些软件库所需要的。

下面我们有一个任务*将 docker 存储库添加到 apt* ，这是非常明显的。它在机器中安装 Docker 的存储库。

在下一个任务中安装 Docker 需要的包的时间。更准确地说，我们将安装以下内容:

*   码头工程
*   docker-ce-cli
*   containerd.io

然后，通过任务 *check docker 处于活动状态*，我们将确保服务在安装后正在运行。我们用任务*检查 docker 组是否就位，确保“docker”组存在*。

此时，我们应该已经在机器上安装了 Docker。但是我们将只能使用 sudo 运行命令，这是不可取的。所以我们运行下一个任务*将 ubuntu 添加到 docker 组*，这基本上将用户 Ubuntu——我们的运行用户——添加到 docker 组。

现在 Docker 已经安装好了，我们可以不用 sudo 来执行命令。但是我们没有 Docker Compose，这也是做我们想做的事情所需要的。接下来的几个任务将安装它。

第一个从服务器下载二进制文件并安装在*/usr/local/bin/docker-compose*下，提供所需的权限。

最后一个只是将二进制文件添加到用户 ubuntu 属性中。

现在我们已经理解了剧本，我们如何执行它呢？

# 如何使用 Ansible 执行剧本文件

Ansible 自带执行剧本的 CLI 工具，就是`ansible-playbook`。

运行以下命令:

```
ansible-playbook -i inventory playbooks/main.yaml
```

仅此而已。Ansible 应该能够连接和安装所有需要的东西。输出将通知您已经运行了哪种操作。

您可以通过再次运行命令来检查 Ansible 实际上是幂等的。什么都不应该改变。

# 安西贝尔仍然和我们在一起

不管 Docker 之类的出现，仍然有一些任务你应该在机器上运行，你不想手工完成。

Ansible 仍然和我们在一起，可以帮助您以可重复、版本化的方式供应机器。

本文原载[此处](https://alexhernandez.info/blog/how-to-install-docker-using-ansible/)。