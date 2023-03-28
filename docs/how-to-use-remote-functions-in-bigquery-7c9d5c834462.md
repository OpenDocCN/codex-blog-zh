# 如何在 BigQuery 中使用远程函数

> 原文：<https://medium.com/codex/how-to-use-remote-functions-in-bigquery-7c9d5c834462?source=collection_archive---------7----------------------->

## 轻松结合 Google BigQuery 和云功能

![](img/267e2530219768716e5ff4fb4b53f34c.png)

在 [Unsplash](https://unsplash.com/s/photos/conifer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上[pisuikan](https://unsplash.com/@pisauikan?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

谷歌在 BigQuery 中为我们准备了一个非常有用的更新，至少目前是在 preview 中。也就是说，你现在可以用 Python、JavaScript 或 Go 等工具在 BigQuery 中创建基于云函数的远程函数。

## 如何创建函数