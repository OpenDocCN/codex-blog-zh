# 基于 ETag 的 Android Glide 图片缓存

> 原文：<https://medium.com/codex/caching-images-based-on-etag-in-android-with-glide-28622d28e5e3?source=collection_archive---------8----------------------->

![](img/a3b16e32b79a65f73bfcfd877fa20592.png)

> 在这篇文章中，你将通过几个步骤学习如何使用带有 Glide 的 ETags 实现图像缓存。

Glide 是 Android 上最著名的库之一，因为它使得在 Android 应用程序中加载图像变得非常容易。它的一个特点是一个缓存系统，以避免多次请求相同的图像，节省时间和网络使用。这种缓存功能是…