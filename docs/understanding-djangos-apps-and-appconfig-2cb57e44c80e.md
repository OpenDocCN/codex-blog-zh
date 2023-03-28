# 了解 Django 的应用和 AppConfig

> 原文：<https://medium.com/codex/understanding-djangos-apps-and-appconfig-2cb57e44c80e?source=collection_archive---------0----------------------->

![](img/ed8cfc281e92acf252814db4c1452e5b.png)

由[保罗·花冈](https://unsplash.com/@plhnk?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 介绍

Django 项目是一组应用程序的集合，它们协同工作来提供一组功能。这个集合由 Django、我们自己和其他开发者提供的应用组成。在一个项目中，属于一个项目的全套应用程序可以在项目`settings.py`中找到，声明为`INSTALLED_APPS`变量。

## 应用程序和 AppConfig 创建