# 有效的 Java:如果需要精确的答案，避免 Float 和 Double

> 原文：<https://medium.com/codex/effective-java-avoid-float-and-double-if-exact-answers-are-required-fbdd9f8b9245?source=collection_archive---------8----------------------->

![](img/0534b0d7287fe7e5d35dd4ccafe189b8.png)

由[杰斯温·托马斯](https://unsplash.com/@jeswinthomas?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

这一章深入探讨了一个有趣的基于计算机科学的话题。当我们需要表示包含小数点的数字时，我们通常会用到`float`或`double`。这些原语类型有助于在各种幅度上表示近似的浮点值。上一句的关键部分是它是一个近似值。我们不能…