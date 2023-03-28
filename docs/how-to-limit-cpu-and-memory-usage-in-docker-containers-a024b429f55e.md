# 如何限制 Docker 容器中 CPU 和内存的使用

> 原文：<https://medium.com/codex/how-to-limit-cpu-and-memory-usage-in-docker-containers-a024b429f55e?source=collection_archive---------1----------------------->

![](img/8180a27f4fcfa614f2c49515993d035a.png)

[Freepik](https://www.freepik.com/free-photo/aerial-view-container-cargo-ship-sea_13180387.htm#query=container&position=12&from_view=search)

> 使用 Docker 容器隔离进程并保证资源

## 介绍

Docker 容器是在一台主机上隔离进程的好方法。这在运行多个 CPU 或内存密集型进程时尤其有用，因为它可以确保一个进程不会耗尽其他进程的资源。

还可以限制 Docker 容器可以使用的 CPU 或内存量。这有助于防止单个进程使用过多的主机资源，或者保证容器总是有最小量的资源可用。

## CPU 限制

可以使用`--cpu-period` `--cpu-quota`参数设置 CPU 限制。`--cpu-period`参数以微秒为单位设置 CPU 周期的长度。`--cpu-quota`参数设置容器在此期间允许使用的周期数。

例如，要将容器限制为使用不超过 50%的单个 CPU 核心，可以使用以下参数:

```
--cpu-period=50000
--cpu-quota=25000
```

您还可以使用`--cpus`参数来设置一个容器可以使用的最大 CPU 数量。例如，要将容器限制为使用两个 CPU，可以使用以下参数:

```
--cpus=2
```

## 内存限制

可以使用`--memory`参数设置内存限制。此参数设置容器可以使用的最大内存量，以字节为单位。

例如，要限制容器使用不超过 512 MB 的内存，可以使用以下参数:

```
--memory=512m
```

您还可以使用`--memory-swap`参数来设置容器可以使用的最大内存和交换空间。该参数的设置方式与`--memory`参数相同，以字节为单位。

例如，要限制容器使用不超过 512 MB 的内存和交换空间，可以使用以下参数:

```
--memory-swap=512m
```

**Docker 命令示例:**

```
docker run --name my-container --cpu-period=50000 --cpu-quota=25000 --memory=512m my-image
```

**示例 docker-compose 配置:**

```
version: '3'

services:
  my-service:
    image: my-image
    cpu_period: 50000
    cpu_quota: 25000
    memory: 512m
```

## 结论

Docker 容器是在一台主机上隔离进程的好方法。可以设置 CPU 和内存限制，以确保一个进程不会耗尽其他进程的资源，或者保证一个容器总是有最小量的资源可供其使用。

[**订阅**](https://narasimmantech.com/#/portal/signup) 我的每周简讯，了解关于 Golang、DevOps、Cloud-Native、Linux、Kubernetes 等的最新见解。

在推特上关注我！