# Python:使用 Pyenv 和 Pyenv-Virtualenv 进行版本管理(Linux)

> 原文：<https://medium.com/codex/python-version-management-with-pyenv-and-pyenv-virtualenv-linux-ecd6578b7bbf?source=collection_archive---------1----------------------->

![](img/70cfe600dfc3d5e8d856f21b6497da65.png)

[Fidias Cervantes](https://unsplash.com/@fidpad?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Pyenv 是在您的开发环境中管理多个 Python 版本的最酷的工具之一。无论是新的机器学习项目还是成熟的产品开发项目，你都会发现`pyenv`非常方便快捷地设置你的 Python 环境。所以让我们直接开始吧。

## 设置 Pyenv 和安装 Python

在这里，我将在运行 Ubuntu 18.04 的 windows Linux 子系统上设置`pyenv`，并安装最新版本的 python，即`3.10.1`(截至创作日期的版本)。

1.  **更新系统**

如果你使用 Ubuntu，这是你在安装任何新东西之前需要采取的第一个自然步骤。在您的 shell 中复制以下命令。

```
sudo apt update -y
```

`sudo`是可选的，取决于帐户和系统对管理员访问的要求。

2.**安装依赖关系**

Pyenv 需要以下依赖项，因此通过在 shell 中复制以下命令来安装它们。

```
apt install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python-openssl git
```

3.**获得 Git 回购**

克隆以下回购。这将让你从 Github 获得最新的`pyenv`版本。

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
```

4.**配置环境变量和环境**

使用以下命令设置 Ubuntu 的`.bashrc`文件中的`PYENV_ROOT`和`PATH`变量。其次设置`pyenv init`进行初始化。在您的 shell 中运行这三个命令。

```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc 
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc 
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.bashrc
```

5.**重启外壳**

现在，您需要使用以下命令重启您的 shell。

```
exec bash
```

或者

```
exec "$SHELL"
```

如果你正在使用`zsh`，那么使用下面的命令。

```
exec zsh
```

6.**确认 Pyenv 已安装**

使用下面的命令`pyenv versions`检查您的`pyenv`是否安装正确，相应的输出如下所示。

```
user@ubuntu:/mnt/c/Users/user$ pyenv versions
* system
```

它显示 python 的当前版本被选为默认版本。

7.**安装 Python**

要列出要安装的 python 的所有可用版本，请使用以下命令。

```
pyenv install --list
```

现在选择你想要的版本并安装。在这里，我正在安装可用的最新版本。该命令通常需要很长时间来运行，因为所选版本的 python 是从源代码构建的。

```
pyenv install 3.10.1
```

安装完成后，运行以下代码进行检查。

```
user@ubuntu:/mnt/c/Users/user$ pyenv versions
* system (set by /mnt/c/Users/user/.python-version)
  3.10.1
```

现在你的`python 3.10.1`已经准备好了。现在让我们设置`pyenv-virtualenv`。

**安装 Pyenv-virtualenv**

现在我们将安装`pyenv-virtualenv`,它帮助我们设置本地开发环境，而不会使 python 的全局安装变得肮脏。

1.  **克隆 Git 回购**

让我们先克隆一下 Github 的`virtualenv` repo。

```
git clone https://github.com/pyenv/pyenv-virtualenv.git $(pyenv root)/plugins/pyenv-virtualenv
```

2.**环境配置**

运行以下命令，以便在 shell 运行时自动启用环境。

```
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
```

3.**重启外壳**

现在，您需要使用以下命令重启您的 shell。

```
exec bash
```

或者

```
exec "$SHELL"
```

如果你正在使用`zsh`，那么使用下面的命令。

```
exec zsh
```

4.**验证安装**

要验证`virtualenv`是否正确安装，运行以下命令。

```
user@ubuntu:/mnt/c/Users/user$ pyenv virtualenv --version
pyenv-virtualenv 1.1.5 (python3.10 -m venv)
```

现在您已经成功安装了`pyenv-virualenv`。

## 创建和使用 Pyenv-Virtualenv

现在让我们看看如何创建和使用`pyenv-virtualenv`。

1.  **创建虚拟人**

运行以下命令创建一个新的`virtualenv`。为了创建环境，我们需要从系统中安装的 python 列表中提供 python 版本。已安装 pythons 的列表可以通过`pyenv versions`访问。第二个参数是虚拟环境的名称。

```
pyenv virtualenv 3.10.1 venv-name-3.10.1
```

现在已经创建了`virtualenv`,通过运行以下命令检查它是否被正确创建。

```
user@ubuntu:/mnt/c/Users/user$ pyenv versions
* system (set by /mnt/c/Users/user/.python-version)
  3.10.1
  3.10.1/envs/venv-name  
  venv-name
```

2.**激活&停用虚拟**

运行以下命令激活`virtualenv`。

```
pyenv activate venv-name
```

若要停用，请运行以下命令。

```
pyenv deactivate
```

要在进入项目的本地开发文件夹时自动激活本地 env，请在文件夹中运行以下命令。

```
pyenv local venv-name
```

现在，这应该会导致以下结果，

```
(venv-name) user@ubuntu:/mnt/c/Users/user/project-folder$
```

现在，如果你离开当前文件夹，你会看到下面的内容

```
(venv-name) user@ubuntu:/mnt/c/Users/user/project-folder$ cd..
user@ubuntu:/mnt/c/Users/user$
```

在本教程中，你学习了如何安装`pyenv`，不同版本的 python 和`pyenv-virtualevn`。毫无争议地享受您的下一个 python 项目。

*我是一名在 GCP、SQL 和 Python 堆栈方面经验丰富的数据工程师，如果您需要帮助来为您的初创公司或下一个商业项目建立数据基础设施，请随时通过我的* [*Linkedin*](https://www.linkedin.com/in/yuvrender-gill/) *联系我。*