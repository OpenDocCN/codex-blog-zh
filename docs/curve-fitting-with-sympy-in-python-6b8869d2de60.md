# 用 Python 中的 Scipy 进行曲线拟合

> 原文：<https://medium.com/codex/curve-fitting-with-sympy-in-python-6b8869d2de60?source=collection_archive---------4----------------------->

![](img/9ee7d1fe67b032da63ecf870e58174a6.png)

克里斯·利维拉尼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

曲线拟合经常被用来模拟真实世界的系统或观测值。给定一组通过某种方式收集的输入数据——通过实验、人口普查、政府档案、用户输入的数据库等。—我们正在寻找的是找到一个能最好地模拟这些数据的函数(称为目标函数)。

有许多类型的目标函数。最简单的曲线拟合函数…