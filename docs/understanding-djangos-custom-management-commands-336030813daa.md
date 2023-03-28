# 理解 Django 的定制管理命令

> 原文：<https://medium.com/codex/understanding-djangos-custom-management-commands-336030813daa?source=collection_archive---------3----------------------->

![](img/e8f71c6b5ff57806dd975dbb893623e5.png)

由[Nathana rebou as](https://unsplash.com/@nathanareboucas?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

# 介绍

Django 是一个免费的开源 python 后端 web 框架，它消除了单调乏味的工作，并提供了几乎每个 web 应用程序都需要的特性。它提供了大量现成的管理命令，允许用户完成重要的任务。其中，几乎每个人都用过`makemigrations`、`migrate`和`createsuperuser` …