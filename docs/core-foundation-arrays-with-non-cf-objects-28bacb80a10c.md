# 核心基础—包含非 CF 对象的阵列

> 原文：<https://medium.com/codex/core-foundation-arrays-with-non-cf-objects-28bacb80a10c?source=collection_archive---------12----------------------->

包含非 CF 对象的`CFArray`教程

![](img/fa43ba56bfff4b4fc95e24a114db7854.png)

图片来自 [MustangJoe](https://pixabay.com/users/mustangjoe-2162920/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1236581) 来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=1236581)

# 介绍

在之前的两篇文章中([使用数组](/codex/core-foundation-working-with-arrays-ca89a2685026?source=your_stories_page-------------------------------------)和[使用可变数组](/codex/core-foundation-working-with-mutable-arrays-cea73b913b5e?source=your_stories_page-------------------------------------))，我们在`CFArray`(或`CFMutableArray`)中存储了核心基础对象，尤其是`CFString`对象。在当前的帖子中，我们将存储非 CF 对象。

# 源代码