# 医疗保健中的人工智能-近距离观察！

> 原文：<https://medium.com/codex/ai-in-healthcare-closer-look-86dadb1fbfc2?source=collection_archive---------15----------------------->

![](img/efe938f1b4168440d524e8f474f1426e.png)

由[肯德尔](https://unsplash.com/@hikendal?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我将带您了解在医疗诊断中使用人工智能的一些实际考虑因素和挑战。除了核心 AI/ML 实践之外，这个领域所涉及的关键性使得关注这些挑战变得很重要。

## **数据集考虑事项**

1.  **不平衡的数据集**—没有疾病的数据样本**比有疾病**的数据样本**数量多，这导致了不平衡的数据集，并因此在医学分析的训练算法中提出了问题。**

**解决方案:**

a)重新采样数据(过采样、欠采样等。)

b)修改损失函数，即使用加权损失来包含不平衡类别的影响。

2.**小训练数据集** —缺乏标记数据集或小训练数据可用性，特别是对于图像分析任务，如使用胸部 x 射线的胸部疾病检测、使用 MRI 扫描的脑肿瘤检测等。

**解决方案**:

a)迁移学习——迁移学习意味着将为一个问题训练的网络用于另一个问题。通过迁移学习，我们可以用几百张图像建立良好的分类器。点击此处了解有关迁移学习[的更多信息。](https://towardsdev.com/transfer-learning-using-pytorch-cbf61b91486e)

b)数据扩充-我们可以对数据使用不同的转换来增加训练数据的广度。在医学图像数据转换的情况下，我们唯一需要小心的是选择转换，记住转换后的 Y 标签(基本事实)仍然是真实的。

例如，如果在胸部 x 光检查中，使用了水平翻转，将导致心脏出现在不同的一侧，这意味着一种单独的医学问题。在这里找到更多关于数据扩充的信息。

![](img/4110e7dd78bc8649e45fb4ee26ffe7d9.png)

图像[来源](https://singularityhub.com/2019/02/20/the-pediatric-ai-that-outperformed-junior-doctors/)

## **模型测试注意事项**

*   **患者重叠** -在创建训练、验证和测试集时，应确保这些集中没有患者重叠，因为由于训练和测试集中存在相同的患者，该问题可能会导致过于乐观的测试结果。*应该根据患者进行拆分来解决这个问题。*
*   **决定基本事实** -决定样本的基本事实是一个不同的挑战，可能会由于观察者之间的分歧而产生，其中一个从业者的意见可能与另一个不同，这在医学领域很常见。共识投票是解决这一问题的一种方式，在这种投票中，一组人类专家被认为可以确定事实真相。

## 实际问题

*   **不同人群的特征** —对于使用医疗数据的人工智能模型来说，实现可靠的泛化是一项挑战。例如，印度的胸部 X 射线数据可能与美国有很大不同，因此基于美国患者数据建立的模型在其他地区可能不太有效。
*   **外部验证** —虽然人工智能模型建立在历史数据的基础上，但它在现实世界数据上的工作方式标志着人工智能模型是可以直接使用还是需要根据新数据进行微调。然而，为了理解 AI 模型在现实世界中的效用，这些需要应用于现实世界的数据(前瞻性数据)。

至此，我们已经了解了数据集挑战、模型测试和医疗保健用例的实际实施挑战。

**参考文献**:

*   [https://www . coursera . org/learn/ai-for-medical-diagnosis/ungradedLab/gjDcc/counting-labels-and-weighted-loss-function](https://www.coursera.org/learn/ai-for-medical-diagnosis/ungradedLab/gjDcc/counting-labels-and-weighted-loss-function)
*   [https://www . coursera . org/learn/ai-for-medical-diagnosis/lecture/vnko 2/multi-task-loss-dataset-size-and-CNN-architectures](https://www.coursera.org/learn/ai-for-medical-diagnosis/lecture/VNkO2/multi-task-loss-dataset-size-and-cnn-architectures)
*   [https://www . coursera . org/learn/ai-for-medical-diagnosis/lecture/RVBZK/measuring-patient-outcomes](https://www.coursera.org/learn/ai-for-medical-diagnosis/lecture/RVBZK/measuring-patient-outcomes)