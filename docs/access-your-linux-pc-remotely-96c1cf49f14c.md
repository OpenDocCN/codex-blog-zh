# 远程访问您的 Linux PC

> 原文：<https://medium.com/codex/access-your-linux-pc-remotely-96c1cf49f14c?source=collection_archive---------4----------------------->

![](img/02c63dcfc53c49cae16ebe23e90e5ebe.png)

您想从另一台机器访问您电脑上的本地文件吗？这是一个简单的使用 SSH 登录你的电脑的演示。SSH 代表安全套接字外壳，它允许用户在不安全的网络上从一台计算机访问另一台计算机。您可以将您的 Linux PC 配置为启用 SSH，并在另一台 Windows/Linux/Mac PC 上访问它的文件。按照以下命令设置您的 SSH 服务器:

首先，通过运行以下命令确保您的软件是最新的:

```
$ sudo apt update$ sudo apt upgrade
```

下一步是安装 SSH 服务器，运行命令:

```
$ sudo apt install openssh-server
```

安装完成后，您可以使用以下命令检查服务器的状态:

```
$ sudo systemctl status ssh
```

如果它显示“**活动:(运行)**”，您就可以开始了。如果它处于非活动状态，请使用以下命令启动服务器:

```
$ sudo systemctl start ssh
```

您可以使用以下命令随时停止/重启服务器:

```
$ sudo systemctl stop ssh$ sudo systemctl restart ssh
```

如果你正在使用 Ubuntu，由于防火墙配置工具你需要打开一个 ssh 端口，下面的命令将允许你 SSH 进入机器:

```
$ sudo ufw allow ssh
```

现在，您的机器可以进行 SSH 访问了。用任何操作系统抓取另一台机器。我将使用一台 mac 机器 ssh 到我的 Linux PC。对于 Linux 和 Mac，您不需要任何额外的配置，但是如果您在 Windows 上，您将需要下载一个 ssh 客户端。我推荐安装 puTTY，这是最流行的免费 SSH 客户端。

成功下载和安装后，您将看到以下屏幕。端口应该是 22，输入您的 Linux 服务器的 IP 地址，然后连接。它会提示您是否信任该主机，只需单击是。下一步是登录。它会要求您输入用户名和密码。

您的用户名是根用户的名称，密码是您的 Linux 机器的根密码。如果你登录成功，你会在终端底部看到**用户名@机器名**。

如果您使用的是 Linux/Mac，只需打开终端，键入以下命令通过 ssh 连接到您的服务器:

```
$ sudo ssh username@ipaddress
```

username 是您服务器的根用户的名称，ipaddress 也是您服务器的名称。接下来，它会提示您输入密码，这是服务器的 root 用户的密码。同样，如果你成功了，你的提示会变成**用户名@机器名**，现在你就可以在客户端电脑上访问你服务器电脑上的所有文件了。

要知道您的 Linux 服务器的 IP 地址，您可以使用下面的命令。突出显示的部分显示所需的 IP 地址:

```
$ ip route
```

所以，我们的远程登录是成功的。我们可以在自己的电脑上访问服务器上的所有文件。接下来，我们将看看如何将文件从本地 PC 传输到远程服务器，反之亦然。

首先，让我们看一下从本地电脑拷贝文件到远程服务器的过程。为此，我们使用 SCP 命令，它代表安全拷贝。要传输文件，请确保您已经退出 SSH 登录，如果没有退出，请键入，

```
$ exit 
```

现在，使用以下命令:

```
$ scp path_to_file remoteuser@remoteserver:/path_to_remote
```

在这里，path_to_file 是您的文件在本地 PC 中的完整路径。remoteuser 是远程 PC 中 root 用户的用户名，remoteserver 是远程 PC 的 IP 地址。如果未指定 path_to_remote，它会将文件复制到主目录。

要一次复制多个文件，可以在一个命令中指定多个文件的路径:

```
$ scp path1 path2 path3 remoteuser@remoteserver:/path_to_remote 
```

要递归复制文件夹中的所有文件，请使用参数-r:

```
$ scp -r path1 remoteuser@remoteserver:/path_to_remote
```

现在，为了将文件从远程服务器传输到本地 PC，我们使用相同的命令，但是参数的顺序颠倒了:

```
$ scp remoteuser@remoteserver:/path_to_remote path_to_file
```

就是这样！！远程访问您的 Linux 机器并将其用于多种目的就是这么简单。但是请记住，按照本教程，您的 Linux 机器将暴露在互联网上，拥有您的根密码的人将能够访问您的个人电脑上存储的所有文件。为了避免这种威胁，一个好的做法是生成访问密钥。同样的细节将在下一篇文章中介绍。看我以前的文章[这里](https://mansik16.medium.com/)。敬请关注更多科技文章:)