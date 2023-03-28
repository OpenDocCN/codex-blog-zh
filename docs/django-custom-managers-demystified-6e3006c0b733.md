# Django 客户经理揭秘

> 原文：<https://medium.com/codex/django-custom-managers-demystified-6e3006c0b733?source=collection_archive---------1----------------------->

![](img/5d80643ea35f26fb510e93fb11a6ab25.png)

卡斯帕·卡米尔·鲁宾在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Django 对象关系映射器(ORM)是一个非常强大的工具。对于 Django 开发人员来说，这是与数据库交互的最简单的方式。开发人员与 ORM 的交互和与 Django 视图的交互一样频繁。

模型管理器是 Django ORM 的一个主要工具，开发人员用它来与数据库交互。从根本上说，管理器是一个 Python 类，它继承了在…