# 在 dockerized Django 开发环境中使用持久数据

> 原文：<https://medium.com/codex/using-persistent-data-in-a-dockerized-django-development-environment-cec794db32a0?source=collection_archive---------11----------------------->

在 Docker 容器中运行 Django，镜像您的代码库，获得一个干净的开发环境，实时反映代码变化。

![](img/e2fee5731fcb6194caf1adf5f0ea8a9c.png)

[伊恩·泰勒](https://unsplash.com/@carrier_lost?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

# 发展环境

拥有一个干净的开发环境是至关重要的，在这个环境中，您可以准确地指定正在使用的系统和包。它可以防止安装在主机上的包和用于您的项目的包之间的干扰。Docker 容器提供了一个有限的、明确指定的和可复制的环境，主机上的文件更改不会影响容器。然而，在开发过程中，我们的代码库不断变化。为了反映这些变化，我们的容器需要不断地重新构建和运行。

## Djangos 开发服务器

Django 运行自己的开发服务器，监控文件变化，并在必要时重启。由于与外界隔离，这些文件更改不会发生在传统的容器设置中。

## 本地数据库设置

此外，使用像 SQLite3 (Djangos 默认)这样的本地数据库文件，每次容器停止(或重启)时，所有像迁移或数据库条目这样的更改都将被清除。迁移可以重新运行，尽管这是一项非常繁琐的任务。其他数据将完全丢失。

## 将卷装入容器

将项目文件夹和数据库作为一个卷安装到 Docker 容器中，可以对其进行读写访问。换句话说，现有数据(我们的项目文件和数据库)可以被容器访问和编辑，新数据也可以被永久保存。

# 设置环境

## 我们的项目结构

我们希望挂载项目文件夹的全部内容，包括我们正在使用的数据库文件，用于本地开发。

```
|-django_docker_setup
|---django_docker_setup
|---example_application
|-----migrations
|-manage.py
|-requirements.txt
|-Dockerfile
|-Database-File
```

我们项目的需求列表

## 我们的文档

运行 Django 应用程序的 Dockerfile

# 构建和运行我们的容器

## 装载卷

[挂载卷](https://docs.docker.com/storage/volumes/)是使 Docker 容器使用或生成的数据持久化的首选机制。

为了将文件夹挂载到我们的容器中，我们使用了`docker run`上的`-v or --volume flag`,并指定了起始路径和目标路径:

```
docker run -v <Path of folder to mount>:<Path to mount inside container>
```

## 发布端口

为了从容器外部访问我们的 Django 应用程序，我们需要向主机发布一个端口。在本例中，我们将之前公开的容器端口(8000)映射到主机的 HTTP 端口(80)。我们使用的格式如下，更多的可能性可以在 [Docker 文档](https://docs.docker.com/engine/reference/run/#expose-incoming-ports)中找到。

```
docker run -p hostPort:containerPort
```

## 完整命令:

```
docker build -t container-name .
docker run -v $(pwd):/home/ -p 80:8000 container-name
```

因为我们的 Dockerfile 位于项目文件夹中，所以我们希望将该文件夹中的所有数据挂载到我们在 Dockerfile 中指定的工作目录中。

*   打印当前工作目录的路径
*   `/home/`是绝对路径，如我们的 docker 文件中所定义的

## 访问正在运行的容器

此时，您的 Django 项目正在您的容器中运行。要访问容器，我们可以使用`[docker exec](https://docs.docker.com/engine/reference/commandline/exec/)`。它在一个运行容器中运行一个命令，允许我们打开一个 shell。

首先，我们需要通过运行以下命令找到我们的容器 id:

```
docker ps
```

这给了我们这样的答案:

```
CONTAINER ID   IMAGE                        COMMAND                  CREATED         STATUS         PORTS                      NAMES3fe54fa54c56   container-name   "/bin/sh -c 'python3…"   2 minutes ago   Up 2 minutes   0.0.0.0:80->8000/tcp       priceless_robinson
```

有了容器 id `3fe54fa54c56`,我们现在可以打开我们的外壳，运行我们的迁移或我们想要的任何东西:

```
docker exec -it 3fe54fa54c56 bash
```

在内部，我们现在可以以持续的方式进行或运行迁移:

```
dockeruser@3fe54fa54c56:/$ python3 manage.py migrate
```

## 运行时更改文件

此外，运行时所做的代码更改将会实时反映在容器内部。Django 应用程序会立即注册在容器外更改文件，然后开发服务器会重新启动。

# 摘要

通过这几个简单的步骤，您创建了一个不干扰您的主机设置的环境，同时保持了开发过程的顺利和简单。该环境被限制在您的主机系统之外，被明确指定且可重复。与此同时，容器外部的文件更改会立即反映到容器内部，而容器内部的更改(如迁移或数据库条目)会在当前运行的容器生命周期之后保持持久。