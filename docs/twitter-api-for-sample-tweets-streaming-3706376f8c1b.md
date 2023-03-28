# 样本推文流的 Twitter API

> 原文：<https://medium.com/codex/twitter-api-for-sample-tweets-streaming-3706376f8c1b?source=collection_archive---------4----------------------->

![](img/4a2de93ed603d35e2fff28b1ee116bac.png)

[国家癌症研究所](https://unsplash.com/@nci?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Twitter 提供了两个流 api 来收集持续访问的 tweets 流。这意味着推文会一直出现，直到被明确停止。这与其他 API 不同，在其他 API 中，每次调用都返回固定数量的 tweets。首先是过滤流，我们首先在其中创建一组规则。然后，我们可以按照这些规则传输推文。一旦有消息，推文将会持续发布…