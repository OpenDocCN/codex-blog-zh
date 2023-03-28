# 使用 NLPAUG Python 库实现 NLP 中的数据扩充

> 原文：<https://medium.com/codex/hands-on-data-augmentation-in-nlp-using-nlpaug-python-library-ad323c22908?source=collection_archive---------5----------------------->

> ***一个意思相同的句子可以有多种写法。***

![](img/72ab52088c0d4286d67055caca2da9bc.png)

照片由[布雷特·乔丹](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

在本文中，我将深入探讨如何实际使用令人惊叹的 nlpaug 库。我假设读者已经很好地理解了什么是数据扩充技术，以及为什么实际使用它。如果不是，那么这是我对数据扩充的理解，

> 数据扩充是一种正则化技术，通过从现有样本生成新样本来增强数据。这增加了数据的多样性，有助于模型更好地概括和减少过度拟合。

为了更好的理解，我在之前的文章中总结了一篇研究论文。一定要读一读，这里的链接是。

# 自然语言处理中的数据扩充

自然语言处理中的数据扩充是指修改现有句子以获得与现有句子相似的新句子。数据扩充技术的准确性是根据新生成的句子与生成它的句子的相似程度来衡量的。意义上的巨大偏差会产生严重的后果。例如，在情感分析任务的情况下，过度修改句子可能最终反转生成句子的情感。

**NLP 中数据扩充的需求**

1.  拥有不平衡的数据集将无助于模型的泛化。模型的学习将更多地受到多数阶级的影响，因此模型将在大多数情况下预测多数阶级。这种型号不能用于生产。因此，为了解决这个问题，我们可以使用数据扩充来为少数类生成新的样本。这种技术被称为**过采样**。
2.  小数据集无法在 Bert、Roberta 等重模型上训练。由于这些模型有数百万个参数，它们需要大量数据来进行归纳，并从数据中获得一些有意义的见解。我们可以使用数据扩充技术生成新的样本，并扩展我们的数据集。
3.  可以用来增加数据的多样性。很多时候，模型有过度适应训练集的趋势。增加数据的多样性将有助于模型更好地泛化。

**探索自然语言处理的数据扩充技术**

在开始研究这些技术之前，有几个控制增强过程的参数，这些参数在所有技术中都是通用的，理解起来非常重要。

*   **lang**(*str*)——你的文字语言。默认值为“eng”。
*   **aug_p** ( *float* ) —将被增加的单词的百分比。
*   **aug_min** ( *int* ) —要扩充的最小字数。
*   **aug _ max**(*int*)—将增加最大字数。如果没有通过，通过 aup_p 计算增加数。如果 aug_p 的计算结果小于 aug_max，将使用 aug_p 的计算结果。否则，使用 aug_max。
*   **停用词** ( *列表* ) —将从扩充操作中跳过的词的列表。

现在我们知道了控制增强技术的参数，让我们深入到实现部分。从现有的句子出发，有很多方法可以创造句子。让我们来看看最流行和最直观的方法及其实现。我将使用 python 的 NLPAUG 库。

安装 NLPAUG 执行下面给出的命令并导入必要的库。

```
!pip install nlpaugimport nlpaug.augmenter.word as naw
```

*   **同义词替换:**从句子中随机选择 *n* 个不是停用词的单词。用随机选择的同义词替换这些单词。

```
syn_aug = naw.SynonymAug(aug_p=0.3)
sentence = 'climate change puts the squeeze on wine production'
mod_sentence = syn_aug.augment(sentence, n=1)
print('Original text :', sentence)
print('Augmented text :', mod_sentence)
```

原文:**气候**变化**给**葡萄酒生产带来压力

增强文本:**气候**改变**位置**对葡萄酒产量的挤压

*   **随机互换:**随机选择句子中相邻的两个单词，互换位置。

```
sentence = 'climate change puts the squeeze on wine production'
aug = naw.random.RandomWordAug(action='swap', aug_p=0.3)
mod_sentence = aug.augment(sentence, n=1)
print('Original text :', sentence)
print('Augmented text :', mod_sentence)
```

原文:**气候变化**使**受到挤压**对**葡萄酒生产**

补充文字:**改变气候**把**挤在**上生产酒****

*   **随机删除:**随机删除句子中的每个单词。

```
sentence = 'climate change puts the squeeze on wine production'
aug = naw.random.RandomWordAug(action='delete', aug_p=0.3)
mod_sentence = aug.augment(sentence, n=1)
print('Original text :', sentence)
print('Augmented text :', mod_sentence)
```

原文:气候**变化**给**葡萄酒生产带来压力**

补充文字:气候影响葡萄酒生产

*   **反义词替换:**从句子中随机选择 *n 个*不是停用词的单词。用随机选择的一个反义词替换这些单词。

```
sentence = 'climate change puts the squeeze on wine production'
aug = naw.AntonymAug(aug_p=0.3)
mod_sentence = aug.augment(sentence, n=1)
print('Original text :', sentence)
print('Augmented text :', mod_sentence)
```

原文:气候变化**给葡萄酒生产带来压力**

补充文字:气候变化**消除葡萄酒生产的压力**

我在本文中介绍了四种技术。扩充时唯一要记住的是，句子在扩充前后的极性保持不变。一定要浏览该库的官方文档，以熟悉更多的技术。分享了参考资料部分的链接。

感谢您阅读这篇文章。我希望你喜欢阅读！

**参考文献:**

*   [https://www . ka ggle . com/code/andypenrose/text-augmentation-with-nlpaug/notebook](https://www.kaggle.com/code/andypenrose/text-augmentation-with-nlpaug/notebook)
*   [https://nlpaug . readthedocs . io/en/latest/augmenter/word/antonym . html](https://nlpaug.readthedocs.io/en/latest/augmenter/word/antonym.html)
*   [https://medium . com/swlh/data-augmentation-in-natural language-processing-756 e 942 b42 f 3](/swlh/data-augmentation-in-natural-language-processing-756e942b42f3)

如果你还没有看过我最近写的关于如何实现 word2vec 的博客，那么看看下面的链接吧！！

[](/swlh/word2vec-in-practice-for-natural-language-processing-a179b3286a21) [## 自然语言处理实践中的 Word2Vec

### 在深入 Word2Vec 之前，我们必须知道什么是真正的单词嵌入，以及为什么需要它。所以让我们…

medium.com](/swlh/word2vec-in-practice-for-natural-language-processing-a179b3286a21)