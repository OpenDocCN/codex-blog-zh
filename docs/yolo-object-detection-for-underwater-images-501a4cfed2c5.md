# 水下图像的 YOLO 目标检测

> 原文：<https://medium.com/codex/yolo-object-detection-for-underwater-images-501a4cfed2c5?source=collection_archive---------6----------------------->

## YOLOv1 实现基于原始论文 [1](https://ipolymenis.xyz/blog/%5Bhttp://arxiv.org/abs/1506.02640%5D(https://arxiv.org/pdf/1506.02640v5.pdf))

![](img/692a8d93c1ea132c6b6c0f7ed0224264.png)

由[彼得罗·郑](https://unsplash.com/@pietrozj?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/deep-learning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

YOLO 是一种对象检测算法，它使用从 CNN 网络学习的特征来检测对象。当执行对象检测时，我们希望在图像中正确地识别给定图像中的对象。目标检测算法中的大多数经典方法使用滑动窗口…