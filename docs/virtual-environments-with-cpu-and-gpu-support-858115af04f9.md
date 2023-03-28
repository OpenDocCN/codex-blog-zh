# 支持 CPU 和 GPU 的虚拟环境

> 原文：<https://medium.com/codex/virtual-environments-with-cpu-and-gpu-support-858115af04f9?source=collection_archive---------4----------------------->

![](img/b081c1ca6a11f1c7c842ca080313c884.png)

Photo by [卡晨](https://unsplash.com/@awmleer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/gpu?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# CPU 和 GPU 的 Anaconda 配置

部署机器学习和深度学习项目并不总是直截了当的，特别是当我们需要在 GPU 中运行模型时，如果我们的机器中有一个的话。首先，我们需要有一个支持 CUDA 库的 GPU。CUDA 支持的 GPU 列表可以在[这里](https://developer.nvidia.com/cuda-GPUs)找到。下一步是指定…