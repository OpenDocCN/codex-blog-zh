# r:将数据帧合并成更大的矩阵

> 原文：<https://medium.com/codex/r-merging-dataframes-towards-a-greater-matrix-a88ff1c74707?source=collection_archive---------6----------------------->

## 当限于整体数据的子集时，学会组合数据框。

![](img/f7f1eb1e846778627d25487022a61b55.png)

斯科特·桑克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

对于数据科学来说，R 中有许多有价值的工具。最近，我一直在使用 LDLinkR 为我的研究收集连锁不平衡统计数据。我很快发现，这个算法每次迭代最多只能处理 1000 个 SNP。鉴于…