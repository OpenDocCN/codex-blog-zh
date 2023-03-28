# 地形条件逻辑和动态配置块

> 原文：<https://medium.com/codex/terraform-conditional-logic-and-dynamic-configuration-blocks-19c3c3f62796?source=collection_archive---------1----------------------->

![](img/eb7ca27fed374839dc9cb06492c91787.png)

约书亚·阿拉贡在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

条件逻辑可用于需要仅在需要时为特定资源定义部分 Terraform 配置的情况。

此外，当一些资源类型在其参数中包含可重复的*嵌套块*时，这些将需要在您的模块中动态处理。动态地…