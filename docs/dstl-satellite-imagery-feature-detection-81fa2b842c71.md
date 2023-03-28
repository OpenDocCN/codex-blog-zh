# Dstl 卫星图像特征检测

> 原文：<https://medium.com/codex/dstl-satellite-imagery-feature-detection-81fa2b842c71?source=collection_archive---------11----------------------->

![](img/80edeccf1658d543db9ff38047e25f29.png)

[卡格尔](https://www.kaggle.com/competitions/dstl-satellite-imagery-feature-detection/data)

# 目录:

> ***1。商务问题***
> 
> ***2。*数据概述**
> 
> ***3。绩效指标***
> 
> ***4。EDA***
> 
> ***5。现有方法***
> 
> ***6。数据预处理***
> 
> **7*7。造型***
> 
> ***8。*错误分析**
> 
> ***9。部署***
> 
> **10*。未来工作***
> 
> ***11。参考*和**

# 1.商业问题

分析卫星/航空图像在诸如灾害管理、国防、监测全球变暖的影响、城市规划等各种领域中发挥着重要作用。通过将这个领域与深度学习/计算机视觉相结合，所有这些事情都可以实现自动化。

在这里，物体识别可能是分析卫星图像的主要任务。在当前的场景中，由于硬件(CPU 和 GPU)和深度学习技术的进步，这可以更准确地完成

本博客包含上述类似问题陈述的工作，实际上来自 [Kaggle](https://www.kaggle.com/competitions/dstl-satellite-imagery-feature-detection/overview) ，其中提供了各种波段的 1 公里 x 1 公里卫星图像，我们的目标是检测和分类该区域中发现的物体类型

# 2.数据概述

## **2.1。train_wkt.csv**

所有训练标签的 WKT 格式

ImageId —图像的 ID
class type—对象的类型(1–10)
multi polygon wkt—标记区域，是以 WKT 格式 WKT - >链接表示的多多边形几何图形

## **2.2。three_band.zip**

3 波段卫星图像的完整数据集。

## **2.3。十六 _band.zip**

16 波段卫星图像的完整数据集。

![](img/abd82f42a8a14c59c99544357fbd02d5.png)

法官

## **2.4。grid_sizes.csv**

所有图像的网格大小

ImageId —图像的 ID
Xmax—图像的最大 X 坐标
Ymin—图像的最小 Y 坐标

## **2.5。train_geojson.zip**

所有训练标签的 geojson 格式(本质上这些是与 train_wkt.csv 相同的信息)

## **2.6。类别标签**

1.  建筑物——大型建筑、住宅、非住宅、燃料储存设施、加固建筑
2.  杂项人造结构
3.  路
4.  轨道——差/泥土/大车轨道、人行道/小道
5.  树木——林地、树篱、树木群、独立树木
6.  作物——等高耕作/耕地、谷物(小麦)作物、行作物(土豆、芜菁)
7.  航道
8.  死水
9.  大型车辆—大型车辆(如货车、卡车、公共汽车)、物流车辆
10.  小型车辆—小型车辆(汽车、货车)、摩托车

# **3。绩效指标**

使用 [Jaccard 指数](https://en.wikipedia.org/wiki/Jaccard_index)作为性能指标

![](img/674dcecacb8023b453589182d05a8ee8.png)

[维基](https://en.wikipedia.org/wiki/Jaccard_index)

![](img/a2838a08667b5af8ec739def7810b646.png)

[维基](https://en.wikipedia.org/wiki/Jaccard_index)

# 4.探索性数据分析

**4.1。类别标签的频率**

![](img/7341f050b832b3d5649120d2a43dbc66.png)

条形图

![](img/f0b228557a762956afc7c30d4d97f0d5.png)

类别中多边形数量与图像的热图

***观察:***

1.  有 25 个独特的图像
2.  所有图像都有一个对象树
3.  水道只出现在一些图像中
4.  几乎所有的图像都有树和轨迹

**4.2。多重多边形**

下面显示了多边形和原始图像之间的一些比较

![](img/868b17713a294dab4be7b9d430ac7b4e.png)![](img/d87099cab9c78c09262ea1432e066c72.png)![](img/9e02a7c60084032360323b6727c0f070.png)![](img/f7af843498bbf9092e79b0b0feea348c.png)

多重多边形

**4.3。分类多重多边形**

下图显示了具有类级多重多边形的图像

![](img/d636f71866f867f0321704bb5da62d86.png)![](img/8f1227d061bbe9c0f29d016c87a13719.png)![](img/9676b0c738e6372c4b8b2d57a5251e4a.png)![](img/f7893ae8639db9bc1c5e8d1f7ba2f840.png)![](img/0116129dc2e6fe8276f43549d6dd670e.png)![](img/11564948b1faeef7b8ca0b8b3f882992.png)![](img/e89de34c571e1c5112e9d4610c5a447a.png)![](img/ca3830bfecf4e8e01351fbcd84f1f832.png)![](img/06ffc9d39bb2cd82b8a718e6019e3a2e.png)![](img/8bf7939225d638b17a766dd8c565dcf4.png)

分类多重多边形

**4.4。物体区域**

![](img/98ba5a2d177f5d7d5cef53070be17259.png)

图像中对象的区域

***观察:***

1.  农作物覆盖面积最大
2.  水路和车辆的区域覆盖率最低

# 5.现有方法

1.  [现有方法 1](http://1.	https://www.researchgate.net/publication/338422718_ISSN_2454-132X_Impact_factor_4295_Application_of_deep_Neural_Networks_for_object_detection_in_satellite_images)

**数据集概述**

![](img/a80e2a0e22c31e6e2e7e678a79b7484c.png)

各种类型图像的波段数

所有图像的大小调整为 3 波段 RBG 图像大小，然后连接。结果包含 20 个 3348 x 3392 的通道。由于该数组太大而无法处理，因此它被转换为大小为 112 x 112 x 20 的面片。修补是在图像和掩模上进行的，并由此训练各种 DNN 架构。多光谱 U-net、倒金字塔模型、PSPNET 等架构。

2.[现有方法 2](https://www.kaggle.com/code/anomsulardi/dstl-semantic-segmentation)

在这种方法中，只有 8 个频带，即 M 频带用于模型的训练。使用的型号是 U_Net。

# 6.数据预处理

这里的主要目标是准备好进一步用于训练模型的数据集。

创建了各种函数，这些函数将获取给定范围的像素值，并从 DataFrame (train_wkt_v4.csv)中提供的 MultipolygonWKT 值中提取遮罩。

从上述功能中，各种图像的遮罩被提取并存储在文件夹 as 中。tif 文件

所有输入图像和蒙版的补丁被创建并存储为。npy 文件

以上 final all_images.npy 和 all_masks.npy 用于训练模型。

# 7.系统模型化

[U_Net](https://arxiv.org/abs/1505.04597v1) 模型在上面生成的数据集上进行训练。

U_Net 模型是众所周知的模型，主要用于分割任务，主要用于医学领域。建筑被称为 U 网主要是因为它的对称形状和许多跳跃连接。

![](img/413f6739f4954d8a93c0a913d9dfda7f.png)

[UNet](https://arxiv.org/abs/1505.04597v1) 建筑

epoch 对 loss 和 epoch 对 Jaccard_coef 的图如下所示。

![](img/e480d0b46fb407a55ba1fcec2f321d83.png)![](img/f170ac30c7012ab12bccecb57435852e.png)

历元对 Jaccard_coef 和历元对损失

可以观察到的一件事是，最后一层包含 sigmoid 激活，并且“二元交叉熵”被用作损失。

这里，在每个像素和每个通道(即类别标签)中，我们将获得一个概率分数。

下面的代码片段显示了每个类标签的最佳阈值的提取。

![](img/cd559467adb6ca5df01d9f9ee0d8d31f.png)

测试数据集上的 Jaccard 得分为 0.67。

## **上述模型的预测**

输入图像被修补，并且在其上的模型预测包含预测掩模的修补。这些预测掩模的补丁被组合并与原始掩模进行比较

![](img/1d0b98e525c7aba4de063fe80f4c0c78.png)![](img/2eb904f97d682d568c3a69ee4bf6a87a.png)![](img/8d6b4f5d92ec1d675c02bab8fa3094ce.png)![](img/92a9aec49565f4f67b04a4dcb98c501b.png)![](img/e266de3bf22e63825e513b3549ec5291.png)![](img/d3da6a3dde9a73c6a9c313fd46e10cb5.png)![](img/7306c3482989f036525a55200352671c.png)![](img/a5177cc04637130bde202664f4247eeb.png)![](img/d1c879d029f6ff90ddfa2d00b3c1e486.png)![](img/46cf38268fd6f6b2c1df4de11bcb104c.png)

原始马克斯 vs 预测面具

# 8.误差分析

提取低 Jaccard 分数(即低于 0.2)的图像 ID，其 EDA 如下所示

![](img/3c8348fafecdd5c613102da823e11628.png)

对于较低的 Jaccard 分数，每个图像的对象数量

提取平均 Jaccard 分数(即在 0.2 和 0.6 之间)的图像 ID，其 EDA 如下所示

![](img/cd002302186b62911b3a9f99d51e8590.png)

对于平均 Jaccard 分数，每个图像的对象数量

***观察:***

1.  Jaccard 分数低主要是由于许多物体(如小型和大型车辆)的区域覆盖非常低。由于这个原因，将会有许多错误的分类，这将会把特定类的 Jaccard 分数拖到 0。这将再次影响完整图像的总体 Jaccard 分数，因为我们平均了所有类别的 Jaccard 分数

# 9.部署

使用 streamlit 部署完整的最终管道

上述管道执行的 Youtube 链接-->

# 10.未来的工作

1.  可以用 SegNet 模型训练数据集，以提高 Jaccard 得分
2.  一个单独的模型可以用来在像车辆这样的小物体上进行训练
3.  完整的 20 个波段可用于训练和预测

# 11.参考

1.  [https://www . ka ggle . com/code/anomsulardi/dstl-semantic-segmentation](https://www.kaggle.com/code/anomsulardi/dstl-semantic-segmentation)
2.  [https://www . ka ggle . com/code/visoft/export-pixel-wise-mask/script](https://www.kaggle.com/code/visoft/export-pixel-wise-mask/script)
3.  [https://www . ka ggle . com/code/drn 01 z 3/end-to-end-baseline-with-u-net-keras/script](https://www.kaggle.com/code/drn01z3/end-to-end-baseline-with-u-net-keras/script)
4.  [https://www.kaggle.com/code/ksishawon/segnet-dstl](https://pypi.org/project/patchify/)
5.  [https://www . Applied ai course . com/course/11/Applied-Machine-learning-course](https://www.appliedaicourse.com/course/11/Applied-Machine-learning-course)

你可以在这里找到我的完整代码——[GitHub Repo](https://github.com/anudeep-j98/DSTL-Satellite-Image-feature-detection)

你可以在 [Linkedin](https://www.linkedin.com/in/anudeep-joshi-b4033b12b) 上联系我