# 在十分钟内建立一个自动的抽象文本摘要

> 原文：<https://medium.com/codex/build-an-automatic-abstractive-text-summarizer-in-ten-minutes-f15f07e54bae?source=collection_archive---------4----------------------->

## 使用 Python 中的变形金刚，火炬，句子片断库。

![](img/1d68c463ad1a9ee7f93f99af3672cbd9.png)

梅尔·普尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**文本摘要**是将长文本缩短为较短文本，同时保留关键元素和含义的过程。手动总结文本会花费很多时间。使用最先进的 **NLP** 模型实现流程自动化可能是解决这一问题的办法。这个问题可以用两种方法来解决

**提取文本摘要**:该方法旨在识别给定文本中最重要的句子，然后将其提取并分组以形成文本的较短版本。

**抽象文本摘要**:这种方法旨在创建一个简明扼要的源文本摘要，抓住要点。产生的概要可以包括在原始文本中找不到的附加短语和句子。

我们将使用谷歌的 **PEGASUS** 模型来完成手头的任务。

![](img/523f37b98d96c26cb663d04afce2dbb9.png)

[苏丹欧阳](https://unsplash.com/@sdoy1995?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

**PEGASUS** 代表使用提取的间隙句子进行预训练，用于抽象的序列到序列摘要模型。他们设计了一个名为 gap 句子生成的预训练自我监督目标函数来训练变压器模型。

## 飞马建筑

飞马采用了**变形金刚**的架构。它使用**编解码器**模型进行 **seq2seq** 学习。文本被并行输入**编码器**，产生一个上下文向量。上下文向量只不过是分配给每个单词的一组数字，因为机器无法理解文本。这个向量然后被馈送到**解码器**，解码器对向量进行解码并产生摘要。要了解更多关于变形金刚的知识，你可以阅读这篇文章[“图解变形金刚”，作者是杰伊·阿拉姆马](http://jalammar.github.io/illustrated-transformer/)

想了解更多关于飞马的信息，可以看看这篇文章。

[](https://ai.googleblog.com/2020/06/pegasus-state-of-art-model-for.html) [## PEGASUS:抽象文本摘要的最新模型

### 学生的任务通常是阅读一份文件并编写一份摘要(例如，读书报告)来展示…

ai.googleblog.com](https://ai.googleblog.com/2020/06/pegasus-state-of-art-model-for.html) 

以下是我们将要遵循的程序的逐步概要:

1.  安装所需的库。
2.  导入所需的库。
3.  初始化模型。
4.  向模型提供输入。
5.  获取摘要文本。

完整的代码可以在[这里](https://github.com/DhirajPrakash22/Abstractive_text_summarization/blob/main/PegasusDemo.ipynb)找到。

# 安装所需的库

让我们安装任务所需的所有库。

取消对代码的注释，安装所需的库。此代码适用于 CPU 版本。如果你想在 GPU 上运行它，访问 PyTorch 网站并安装它的 GPU 版本。

# 导入所需的库

安装完所需的包后，我们需要使用下面几行代码导入它们。

`transformers`库为各种任务提供了各种预训练的 AI 模型。我们使用 Pegasus 模型进行文本摘要。

`torch`是一个开源的机器学习库。它为深度学习提供了广泛的算法。

# 初始化模型

导入所需的库之后，我们就可以初始化模型了。

我们使用在 **XSUM** 数据集上预先训练的模型。

XSUM 数据集是用于评估抽象文档的数据集。要了解更多关于 XSUM 的信息，您可以阅读下面的文章。

[](https://paperswithcode.com/dataset/xsum) [## 带代码的论文- XSum 数据集

### 极限摘要(XSum)数据集是用于评估抽象的单文档摘要的数据集…

paperswithcode.com](https://paperswithcode.com/dataset/xsum) 

**标记化**是将短语、句子、段落或整个文本文档分割成更小单元的过程。这些较小的单位被称为代币。我们使用飞马座标记器来完成这项任务。

使用 from_pretrained 方法存储和检索模型配置。

然后模型被初始化，并可以通过使用模型变量来访问。

# 向模型提供输入

**src_text** 是需要汇总的源文本。您可以将此文本替换为您希望模型汇总的文本。输入不能一次全部输入。我们必须把它分成小批，然后输入模型。然后我们创建另一个名为 **tgt_text** 的变量，它将存储模型产生的输出。

# 获取摘要文本

在模型总结了文本之后，它被存储在 tgt_text 变量中。我们可以通过调用打印函数来查看输出。

您的抽象文本摘要器已经准备好，无需编写数百行代码。我们使用的是模型的预训练版本。如果您需要，还可以针对特定目的对模型进行微调。

# 摘要

下面是我们执行抽象文本摘要的一步一步过程的总结。

1.  安装了所需的库。
2.  导入了所需的库。
3.  已初始化模型。
4.  批量向模型提供输入。
5.  获得了总结文本。

# 参考

*   谷歌人工智能博客:PEGASUS:抽象文本摘要的最新模型
*   [PEGASUS:谷歌最先进的抽象摘要模型](https://towardsdatascience.com/pegasus-google-state-of-the-art-abstractive-summarization-model-627b1bbbc5ce)
*   [杰伊·阿拉玛的《变形金刚》](http://jalammar.github.io/illustrated-transformer/)
*   [抱脸:飞马-XSum](https://huggingface.co/google/pegasus-xsum)