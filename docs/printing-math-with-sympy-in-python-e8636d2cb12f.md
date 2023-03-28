# 用 Python 中的 Sympy 打印数学

> 原文：<https://medium.com/codex/printing-math-with-sympy-in-python-e8636d2cb12f?source=collection_archive---------8----------------------->

![](img/72b8feb22eef567ad2835b36c344fe24.png)

[斯科特·格雷厄姆](https://unsplash.com/@homajob?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

Python 库 sympy 在进行符号计算方面非常出色，但不幸的是结果很难阅读。例如，我想在 k1、k2、k3 和 k4 为常数的情况下找到`u=(k3+k4*t)*cos(k1+k2*t)`对时间的积分(我称之为变量 t)。我的剧本是这样的:

```
from sympy import *
k1,k2,k3,k4 = symbols('k1 k2 k3 k4')
t = Symbol('t',positive=True)
u =…
```