# 用于实体提取的数据分析

> 原文：<https://medium.com/codex/data-analysis-for-entity-extraction-a82328e8027?source=collection_archive---------23----------------------->

![](img/7a93da4cccb88d9a57a9a41c11512c7c.png)

卢卡斯·布拉塞克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

数据分析是机器学习模型可以训练之前的主要步骤之一。这是因为对数据的分析有助于我们找到隐藏的模式，然后可以用来有效地训练机器学习模型。即使在机器开始学习数据之前，我们人类也需要首先理解并适应这些数据，否则可能会导致 GIGO(垃圾输入，垃圾输出)。

以下是在训练实体提取模型来提取业务实体之前要考虑的一些事项。

**领域知识**

理解在文档中使用不同实体的业务环境是很重要的。以人名为例，它可以用在不同的上下文中，比如在医疗文件中作为病人，或者在抵押文件中作为贷款人或借款人等等。实体的含义因文档而异。理解这些细节有助于更好地理解需要从文档中提取实体的上下文，以便对业务有意义。

**序列大小**

像 BERT 这样的预训练模型是无法用超过 512 个单词的序列大小进行训练的。这是一个问题，因为实体可能出现在句子中的任何位置。例如，实体可能出现在句子的开头、中间或结尾。如果一个句子是一个很大的段落，比如说有 1000 个单词，并且在这个句子的末尾存在一个实体，那么在 512 序列长度上训练这个实体就变得很困难，因为在 512 单词长度之后的所有信息都会由于句子在 512 单词长度上被截断而丢失。

Wordpiece tokenizer 使事情变得更糟。例如，在培训开始之前，像“Johnson”这样的单词可能会被拆分为“John ##son”。由于增加了额外的令牌，这进一步增加了序列的大小。

此外，大序列比小序列需要更多的时间来训练。因此，在数据分析期间理解实体的序列长度要求是非常重要的。这有助于在更短的时间内更有效地训练模型。对于存在的超过 512 字长的实体，可以实现混合方法，首先使用预定义的规则缩短序列，使得实体对于 512 序列长度是可见的。

**可训练页数**

有时，在一个有 100 页的文档中，某些实体可能出现在文档的开头，例如在整个训练语料库中的第 1 页或第 2 页。在这种情况下，模型不需要看到所有 100 页来学习一个实体，并且可以仅在该实体出现的前 n 页上进行训练。这有助于减少训练和推断时间，因为模型现在只需要在某些 n 页上训练/推断。

**保留或删除某些标点符号**

总的来说，在训练实体提取模型之前去除标点符号是一个好主意。标点符号增加了数据的随机性，导致 F1 分数较低。但是，您可能希望保留某些标点符号，以区分不同的句子格式。

例如，让我们举一个以两种不同的句子格式出现的实体的例子。

> 句子格式 1 →客户是约翰·史密斯(“借款人”)，国家银行是(“贷款人”)。
> 
> 句子格式 2 →借款人:约翰·史密斯

如果从上面的句子中去掉标点符号，就会导致

> 客户是约翰·史密斯借款人州银行是贷款人
> 
> 句子格式 2 →借款人约翰·史密斯

结果是，由于在句子格式 2 中学习的模式，该模型现在错误地将*国家银行识别为借款人*。

> 句子格式 1 →客户是 John Smith **借款人国家银行**是贷款人
> 
> 句子格式 2 →借款人约翰·史密斯

# **结论**

与任何其他机器学习用例一样，在训练实体提取模型之前，数据分析是最重要的步骤之一。考虑上述因素将有助于您有效地训练模型。它还有助于了解数据中的不同模式，以及从模型推断中可以得到什么。