# 如何在 PySpark 中检查数据质量

> 原文：<https://medium.com/codex/how-to-check-data-quality-in-pyspark-8a882e45bc95?source=collection_archive---------0----------------------->

使用 deequ 计算指标并为您的大数据集设置约束

![](img/703f6ce0bc62e627a4adc58065ce426f.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [Prateek Katyal](https://unsplash.com/@prateekkatyal?utm_source=medium&utm_medium=referral) 拍摄

我们都从我们的同事、我们的利益相关者，有时甚至是我们的客户那里听说过——这些数据是怎么回事？

如果我们可以设置一些检查和约束，并在我们的数据消费者看到问题之前发现问题，而不是从其他人那里听到它，会怎么样？如果我们能在…上这样做呢