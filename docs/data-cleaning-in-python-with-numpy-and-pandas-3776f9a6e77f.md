# 用 NumPy 和 Pandas 实现 Python 中的数据清洗

> 原文：<https://medium.com/codex/data-cleaning-in-python-with-numpy-and-pandas-3776f9a6e77f?source=collection_archive---------3----------------------->

![](img/89389cc83787c1194d7431181445de0f.png)

善良好奇的在 [Unsplash](https://unsplash.com/s/photos/bubbles?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的

一个有抱负的、使用 python 的数据科学家必须学习的第一个实用程序包括 numpy 和 pandas。所有这些都为数据科学家提供了各种各样的工具来应用全面的分析并找到深刻的见解，但是由于内置了如此多的函数和属性，一开始可能很难知道使用哪一个。

一旦数据科学家手中有了好的数据，他们必须首先探索和处理数据，以确保数据便于分析、可视化和机器学习。[根据 Anaconda](https://numpy.org/doc/stable/reference/generated/numpy.where.html) 的一项民意调查，数据科学家 45%的时间用于数据准备任务，比如数据清理。您的里程可能会有所不同，但您应该会发现专注于清洁会产生显著的效果，甚至比巧妙的功能工程或繁琐的超参数调整更有效果。

下面，我概述了一些基本函数，它们可以完成数据清理中的任何任务。若要开始，首先必须通过导入关键包并读入数据来设置笔记本。

```
# import your packages
import numpy as np
import pandas as pd# read in your data
df = pd.read_csv("data/
dataset.csv")
```

# 基本数字和熊猫工具

## 首先-检查空值

![](img/776b12efebc2b5f82321f04f64d153bf.png)

照片由[亨特·哈利](https://unsplash.com/@hnhmarketing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/tools?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

```
df.isna().sum()
```

在任何初始 EDA 中，检查数据中的空值都是首要任务。还有其他一些熊猫方法，比如**。头()** / **。尾巴()**，**。描述()**和**。info()** ，组合使用**。isna()** 后跟**。数据框架变量上的 sum()** 是应该用来检查数据的首要工具之一。它计算每一列中的空值，并报告每一列中缺少值的单元格的总数，这使您可以看到在清理时应该首先注意的地方。

## 操作数据类型

```
df['date'] = pd.to_datetime(df.date)
```

Pandas 系列有统一的数据类型——稍后会详细介绍。可以用 **df.info()** 检查数据帧中每一列内容的数据类型。如果您有一个明确包含日期的列，尝试使用 **pd.to_datetime()** 将数据强制转换为适当的日期时间格式。

类似地，如果您注意到一个列包含数字，但是显示非数字对象作为该列的数据类型，尝试通过使用 **pd.to_numeric()** 将该列强制转换为 int64 或 float64 数据类型。

## 更正条件值

```
np.where()
```

**np.where()** 是用于数据清理的瑞士军刀多工具。它几乎可以用于数据集上的任何纠正操作。它需要三个参数:

![](img/903ab44abd28b3442f013e3abc0e7cad.png)

由[帕特里克](https://unsplash.com/@pf91_photography?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/swiss-army-knife?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

1.  您想要进行的更改必须满足的一个(或多个)条件。
2.  如果满足上述条件，您希望进行的更改的值。
3.  当上述条件*不满足*时应使用的值。

替换空值？你去过了吗？一键编码？你可以用 **np.where()** 做到这一点。管理异常值，修复数据输入错误或使相似的条目一致？ **np.where()** 整天。

## 删除重复项和错误

```
df.drop(index=21500, inplace=**True**) # The index number may be any integer corresponding to the index of the row you would like to drop.
```

**。drop()** inplace 在您想要删除单个行项目时特别有用。每当您识别出明显重复的数据行或错误的数据条目时，请使用此选项。当心仅仅因为你不喜欢内容，或者因为它不符合你的期望而删除行项目——删除行会使你的数据有偏差。

## 使用索引数组。应用()和 lambda

想象一个场景，其中数据集拥有一列，每一行的单元格包含一个项目数组——一个由逗号分隔并用方括号括起来的集合。您希望引用数组中的项目。不幸的是，这样做并不像看起来那么简单。

![](img/1dfbf3f2ecd412ecb147975d84dd029f.png)

照片由[该工程在](https://unsplash.com/@thisisengineering?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) [Unsplash](https://unsplash.com/s/photos/math?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上绘制

通常使用 python，可以通过返回列表变量名，后跟包含所需项目索引号的方括号来引用列表或数组的项目。然而，对于 pandas，DataFrame 列是 pandas *系列，*锁定统一的数据类型，即包含文本和数值的列会将其内容作为“对象”列出。同样，如果一个列包含类似 python 列表的数字项。因此，pandas 会将一个似乎包含数组或列表的单元格评估为一个对象(或字符串)，并且尝试在数据帧中索引这样的数组或列表会简单地返回字符串索引处的*字符*。

如果您想避开这个经常令人沮丧的特性(由于与[矢量化](/analytics-vidhya/understanding-vectorization-in-numpy-and-pandas-188b6ebc5398)相关的原因，它确实是一个特性)，您可以像处理列表一样使用如下表达式来处理对象列内容:

```
df['kcal'] = df.nutrition.apply(**lambda** x: x[1:-1].split(sep=', ')[0])df['fat'] = df.nutrition.apply(**lambda** x: x[1:-1].split(sep=', ')[1])df['sugar'] = df.nutrition.apply(**lambda** x: x[1:-1].split(sep=', ')[2])
```

在这个例子中，我有一个食谱数据集，每行代表一个独特的食谱。数据集有一个“营养”列，每个行项目都有一个浮点数数组。每行数组中该列的第一个数字是食谱的卡路里数，第二个数字是脂肪含量，等等。如果我想将每个食谱的营养统计数据分离到它们自己的列中，我会以上面的方式这样做，为每个营养信息统计数据创建单独的列。

# 最后的想法

![](img/2c2ff031289acdfa28ac47596d81b585.png)

约书亚·厄尔在 [Unsplash](https://unsplash.com/s/photos/beach-sprint?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

## 组织是关键

将您的笔记本分成与跨行业数据挖掘标准流程(CRISP-DM)中的步骤相对应的几个部分，可以为查看您的笔记本的任何人提供渐进的工作流程和易读性。此外，确保您的清理过程按照您最初进行更改的顺序进行迭代，这样，当您在重新启动内核后尝试重新运行笔记本单元格时，您就不会从包含数据清理代码的单元格中获得 python 错误。

Jupyter Notebook 的 nbextensions 对组织非常有用——我总是在打开**目录**(目录)和**可折叠标题**的情况下工作。

## 具有可操作性

当面对数据似乎不正确的困境时，想想什么行动最有意义，做出适当的改变，并在笔记本上写下你的推理。通常，最重要的事情是做出合理的决定，并继续进行清理和执行分析，而不是陷入决策瘫痪，或花费数小时寻找更多信息来支持您的推理。

# 进一步阅读

Dean McGrath 为*走向数据科学*，[数据清洗使用 Python 熊猫](https://towardsdatascience.com/data-cleaning-using-python-pandas-f6fadc433535)

Neelutiwari 负责*分析 Vidhya* ，[数据清理使用熊猫](https://www.analyticsvidhya.com/blog/2021/06/data-cleaning-using-pandas/)