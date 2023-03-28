# 基于 FaceNet 的 iOS 实时人脸识别应用

> 原文：<https://medium.com/codex/ios-real-time-face-recognize-application-base-on-facenet-9114c818dc73?source=collection_archive---------2----------------------->

![](img/8bf65499b9d91ddf415b279e1e73b5c9.png)

[附身摄影](https://unsplash.com/@possessedphotography?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

## [抄本](http://medium.com/codex)

在这篇文章中，我将告诉你如何开发一个简单的 iOS 应用程序可以识别人脸，准确率很高。我对 70 名用户进行了测试。

该应用程序的识别能力基于预训练的[***FaceNe***](https://github.com/davidsandberg/facenet)***t***model*已经在由大约 3.3M 个面和大约 9000 个类*组成的数据集[***vggface 2***](https://www.robots.ox.ac.uk/~vgg/data/vgg_face2/)*上训练。*