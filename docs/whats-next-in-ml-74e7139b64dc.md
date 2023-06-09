# ML 趋势

> 原文：<https://medium.com/codex/whats-next-in-ml-74e7139b64dc?source=collection_archive---------9----------------------->

如果你正在寻找 ML 的下一步，这里有一些机器学习研究和行业的最新趋势。

机器学习已经变得非常流行，现在技术领域的每个人都知道它了。它甚至在不同的领域被快速地应用到行业中。为了客观地看待这个问题，这里有一些来自一篇研究文章的数据。

在预测期内，全球机器学习市场预计将从 2021 年的 155.0 亿美元增长到 2028 年的 1522.4 亿美元，CAGR 为 38.6%。

![](img/fac2822b42bcda368082e9a135358cdb.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 拍摄的照片

以下是我最近发现的一些趋势。

## 联合学习

数据隐私现在比以往任何时候都更加受到重视。 ***可区分隐私*** 的概念被广泛纳入[现实世界应用](https://en.wikipedia.org/wiki/Differential_privacy)。差异隐私是一种公开共享数据集信息的系统，通过描述数据集中的群体模式，同时保留数据集中的个人信息。

为了理解差分隐私，考虑这样一个例子，其中你的医疗记录是机器学习模型的输入。当您在两种情况下训练模型时:一个数据集具有包括您的完整输入，而第二个数据集没有您的病历输入，如果两个模型中的输出差异可以忽略不计，则认为该模型保留了差异隐私。这是因为第三方只能访问模型输出，如果他们不能衡量两种情况之间的差异，他们就不可能预测您的医疗输入。

机器学习仍然对隐私构成严重威胁，因为数据会从我们的设备上传到云端。数据中心可能会受到攻击或其他什么的。因此，一个名为联合学习的新研究领域正在兴起，在这个领域中，你的数据不会离开设备。它的工作原理如下:

*   当前模型被下载到您的设备并用于评估
*   该模型根据您的数据进行改进，来自多个设备的汇总变化(当它们空闲或在无限网络带宽下充电时)被发送回服务器。

## 用户个性化

信息过载是一件令人担忧的事情。我们不想被一个网站上的所有产品或文章拖住。随着数据规模的增长，个性化网站以获得更好的体验是必不可少的。个性化的预期结果是提高客户忠诚度和投资回报率。

因此，大多数公司希望提供定制的用户体验。在我们已经接触过的许多产品中，这是一个有用的特性，而且非常复杂。通过机器学习实现个性化是一种可扩展的方式，通常以产品或内容推荐的形式出现。一些公司甚至创建用户角色档案来显示有针对性的广告、更好的产品推荐和增加参与度。

## 无代码 ML & AutoML

机器学习涉及编码、训练模型，并且必须在公司其他人可以使用它之前部署在平台上。

**无代码 ML** 帮助你快速拥有一个运行模型，无需处理任何代码。简单地说，插入你的数据，只需点击几下鼠标就可以训练出一个 ML 模型。您可以在没有数据科学团队的情况下实现数据驱动，并帮助各种规模的公司。无代码 ML 有助于快速开发，易于使用和开发成本低。

**AutoML** 带来了自动调整神经网络架构和改进数据标签工具的可能性。您可以获得最适合您的数据的模型，而不必编码所有的标准模型和探索所有的超参数。这减少了手动运行多个实验的开发时间。

## **少射、一射、&零射学习**

数据收集对于机器学习项目至关重要。但数据质量被视为该行业对较低性能的最大挑战。即使是大型数据集，如果标注不一致，最终也会表现不佳。

少投、一投、零投学习可以说是受到了人类在现实生活中学习方式的启发。在少量学习中，预测是由少量样本做出的。这可以用于手写中的类似字符识别。在一个镜头中，数据被限制为每个类一个例子。我们可以看到一个人一次，但下一次仍然可以认出他。零起点学习可能看起来很混乱。ML 怎么算出任何初始数据？它应该从外观、特征和功能等信息中进行识别。举个例子，如果一个孩子以前见过一匹马，并在某处读到斑马看起来和马相似，但有黑白条纹，那么他认出斑马是没有问题的。

脚注:

[1][https://www . fortune business insights . com/machine-learning-market-102226](https://www.fortunebusinessinsights.com/machine-learning-market-102226)