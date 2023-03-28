# 使用 Python 进行多重处理

> 原文：<https://medium.com/codex/multiprocessing-with-python-b0af88fe9442?source=collection_archive---------9----------------------->

如果您曾经使用 Python 编程，您可能会遇到这样的情况:您希望通过并行运行多个作业或交错运行它们来加速操作。

![](img/5dae2081e5097c6dfde0547311e816c2.png)

[船员](https://unsplash.com/@crew?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/computer-mutliprocessing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

Python 中的并行和并发机制允许您采用这两种方法。在这篇文章中，我将介绍并行性和并发性之间的区别，以及 Python 如何使用这两种方法