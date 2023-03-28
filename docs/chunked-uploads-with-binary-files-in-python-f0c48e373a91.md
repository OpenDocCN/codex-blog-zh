# 用 Python 分块上传二进制文件

> 原文：<https://medium.com/codex/chunked-uploads-with-binary-files-in-python-f0c48e373a91?source=collection_archive---------0----------------------->

## [法典](http://medium.com/codex)

![](img/804967fcc41d241d166e246515b10151.png)

由[扬·安东宁·科拉尔](https://unsplash.com/@jankolar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/data-files?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在 Python 中有很多很棒的分块上传教程，但是由于某些原因，很多教程都集中在文本文件上。您可能想要上传其他内容，比如视频文件，在这种情况下，您需要处理二进制文件。处理二进制文件是一项稍微不同的任务。在进行分块上传时，您可能会犯很多错误…