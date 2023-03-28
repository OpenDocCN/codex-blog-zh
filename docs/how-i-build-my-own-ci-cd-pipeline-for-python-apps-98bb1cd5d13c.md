# 我如何为 Python 应用构建自己的 CI/CD 管道

> 原文：<https://medium.com/codex/how-i-build-my-own-ci-cd-pipeline-for-python-apps-98bb1cd5d13c?source=collection_archive---------0----------------------->

在我最近的一篇文章中，我向你展示了如何将 Python 应用程序部署到 Azure 虚拟机上。在这篇文章中，我想介绍另一个与 DevOps 方法相关的概念，更具体地说，是与构建、测试和部署这些应用程序的过程相关的概念。

![](img/0859988e77197d09cf6f2ec3d83606c9.png)

由[阿尔瓦罗·雷耶斯](https://unsplash.com/@alvarordesign?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们将要使用的项目位于我的 [github 页面](https://github.com/jpraychev/jraychev-portfolio)上，可以很容易地用……