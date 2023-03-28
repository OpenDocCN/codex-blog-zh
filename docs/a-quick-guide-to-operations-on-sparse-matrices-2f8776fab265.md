# 稀疏矩阵运算快速指南

> 原文：<https://medium.com/codex/a-quick-guide-to-operations-on-sparse-matrices-2f8776fab265?source=collection_archive---------1----------------------->

## 充满零的矩阵非常常见，尤其是在机器学习中。为了提高计算效率，需要记住以下几点。

![](img/63a084dae26724b4a9d495af6b17de71.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

矩阵是一个数字表。矩阵中的一个数字通常被称为一个*元素*，由两个代表其水平(行)和…