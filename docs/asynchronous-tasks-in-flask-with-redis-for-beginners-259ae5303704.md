# 初学者用 Redis 实现 Flask 中的异步任务

> 原文：<https://medium.com/codex/asynchronous-tasks-in-flask-with-redis-for-beginners-259ae5303704?source=collection_archive---------5----------------------->

Flask 中管理后台任务的介绍

![](img/49477090ed106baadc5a71d7205ae772.png)

诺德伍德主题公司在 [Unsplash](https://unsplash.com/s/photos/scanning-computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

你是否曾经在一个网站上请求或提交信息，并立即得到一个加载进度条？不是每个请求都会产生即时反馈。

上传文档、图像、视频等操作可能需要时间来处理，因此最好在…