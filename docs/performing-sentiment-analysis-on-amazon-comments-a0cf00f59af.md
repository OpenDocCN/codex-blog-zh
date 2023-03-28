# 对亚马逊评论进行情感分析

> 原文：<https://medium.com/codex/performing-sentiment-analysis-on-amazon-comments-a0cf00f59af?source=collection_archive---------23----------------------->

## 使用快速文本库预测亚马逊评论的评分

![](img/d119a2707318b3cb16d8468f1c70cb54.png)

克里斯蒂安·威迪格在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

目前，我正在进行一项实验，以发现我在亚马逊购物历史中可能存在的购买偏见，第一步是在产品评论中进行情感分析。为此我使用了[快速文本](https://fasttext.cc/docs/en/supervised-tutorial.html)库进行文本分类，这个模型的创建是基于这篇关于 [Kaggle](https://www.kaggle.com/bittlingmayer/amazonreviews) 的文章。

## **检索数据**

在 Kaggle 链接中，可以下载带有格式化数据的数据集，准备好训练模型。我不得不用西班牙语制作另一个数据集，因为我的账户大部分是西班牙语评论，因为它来自墨西哥。你可以在这里下载西班牙数据集[。](https://drive.google.com/file/d/1C4NM5jl60VS8h3Pk63EZSMN6EJhEwqBH/view?usp=sharing)

要在快速文本中训练模型，您需要以下格式的数据:

```
__label__<X> __label__<Y> ... <Text>
```

基本上，在关键字“__label__”之后定义标签，在所有要定义的标签之后放置与之相关的文本。在这种情况下，我们有标签 1，这意味着评价为 1-2 星，标签 2，这意味着评价为 4-5 星。

为什么我们不包括 3 星级的文章？中性评论会影响我们模型的精度，因为它是情感分析。

## **训练模型**

为了训练我们的模型，我们将使用以下命令:

```
./fasttext supervised -input train.ft.txt -output model_amzn
```

这样我们就可以传递我们的 train.ft.txt 数据，并在 model_amzon 文件中创建我们的模型，监督标签告诉 Fast Text 我们将训练一个[监督分类器](https://www.sciencedirect.com/topics/computer-science/supervised-classification#:~:text=The%20goal%20of%20supervised%20classification,of%20guidance%20by%20the%20user.)。

## **测试模型**

为了测试我们的模型，我们首先需要看看它的准确性。我们将运行以下命令，用 test.ft.txt 数据测试我们的训练模型。这个文件包含我们的模型没有看到的数据，它将尝试预测它的标签。

```
./fasttext test model_amzn.bin test.ft.txt
```

Kaggle 数据集的这个模型的精度是 0.916，这非常酷，我们有一个非常可靠的模型，我们可以在快速文本中使用预测的标签来尝试它，这允许我们使用我们训练的模型来预测我们输入的一些文本的最可能的标签。我将使用亚马逊上关于 Roomba 清洁器的一些评论。

```
./fasttext predict model_amzn.bin -
```

尝试一个非常明显的评论:

> **我讨厌它:这应该是一个自主助手，帮助我保持地板清洁。清洁部分实际上工作得很好。我注意到的第一件事是，对于这样一个小设备来说，它有多大声…..**

我们得到了一个很好的预测标签 1，意味着 1-2 颗星

> **iRobot 675 改变了我的生活。我是 4 个小孩的妈妈，住在一个 1880 平方英尺的房子里，房子坐落在一大片林地上。我的孩子经常在户外活动，会在各种各样的泥土、泥土、树叶、草地等上行走。我的丈夫，虽然我爱他，但他也有点邋遢，有一个坏习惯，不擦鞋子，还从他的项目中带进一吨锯屑。**

一个很好的预测，我们得到了标签 2，意味着 4-5 颗星

总的来说，我们将为我们的情感分析获得可靠的答案，只是请记住，当审查评论时，人们有时会给 5 颗星，并写道它很糟糕，所以如果你打算在这种领域使用它，请记住这一点，以消除你的模型中的噪音。