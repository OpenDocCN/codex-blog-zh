# 在 PyTorch 中保存和加载变换后的图像张量

> 原文：<https://medium.com/codex/saving-and-loading-transformed-image-tensors-in-pytorch-f37b4daa9658?source=collection_archive---------1----------------------->

![](img/fc16b8232b6f558b37d89dcb1aff7263.png)

我一直在研究一个来自 Kaggle 的 [Covid CT 数据集](https://www.kaggle.com/andrewmvd/covid19-ct-scans)，其中包含 20 个被诊断为新冠肺炎患者的 CT 扫描，以及专家对肺部和感染的分割。我的目标是使用 UNet 建立一个细分模型。由于图像较少(只有 20 个；p)，我必须执行数据扩充，之后，我在训练数据集中有 3200 个图像和遮罩。在将这些数据传递给数据加载器并训练模型时，运行单个 epoch 需要 1.5 小时，而 GPU 几乎没有被使用。

经过一番挖掘，我在 [Kaggle](https://www.kaggle.com/c/siim-isic-melanoma-classification/discussion/163481) 上发现，代码花费了更多的时间来获取图像和调整图像大小，加速的最好方法是首先将图像预处理到合适的大小，然后将它们保存为数据集。作为 PyTorch 的新手，我无法理解这个过程。现在我终于有了解决方案，我决定与任何面临同样问题的人分享代码。

# 执行增强

我已经创建了一个自定义的火炬数据集，它将应用一些变换，如翻转，扭曲，随机亮度和对比度，并将图像调整为 224x224。然后分为训练集和值集。

# 保存变换后的张量

现在我们需要将转换后的图像张量保存在 dataset_train 和 dataset_val 中。我们需要遍历数据集，并在其中使用`torch.save()`。

# 编写一个新的数据集来加载转换后的张量

我们现在必须创建一个新的自定义 torch 数据集来加载这些转换后的张量，而不是 jpeg 图像，并将它们传递给数据加载器。模型训练将在这些张量上进行。

# 加载张量进行模型训练

最后，我们加载这些变换图像的张量，并将它们传递给数据加载器进行模型训练。

> 我参考了[这个](https://discuss.pytorch.org/t/save-transformed-resized-images-after-dataloader/56464/11) PyTorch 论坛的讨论来找到解决方案。

我没有在本文中包括必要的包导入，但是我想这些是不言自明的。在使用这种直接上传转换张量的方法时，运行时间已经显著减少到每个时期平均 1-2 分钟。

希望这有所帮助！