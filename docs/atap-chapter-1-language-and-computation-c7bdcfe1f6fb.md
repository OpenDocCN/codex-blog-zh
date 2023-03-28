# Python 应用文本分析:第 1 章——语言和计算

> 原文：<https://medium.com/codex/atap-chapter-1-language-and-computation-c7bdcfe1f6fb?source=collection_archive---------10----------------------->

# 或者，旅程的开始…

![](img/54a0003edf108e37377145fd025814f4.png)

来自 https://www.publicdomainpictures.net/en/view-image.php?image = 113151&picture =四元素

我在 2019 年亲自参加 [PyCon](https://us.pycon.org/) 的时候买了 Bengford，Bilbro & Ojeda 的*用 Python* 应用文本分析的实体副本。2019 和亲自参加会议感觉是很久以前的事了！

我意识到我怀念和人类语言数据打交道的日子。我有计算机科学和语言学的背景，自 2006 年获得语言学硕士学位以来，我一直在努力寻找一份能够利用这两个领域技能的工作。作为一名后端开发人员，我很满意，但是上周我和同事进行了至少三次对话，其中出现了一些语言学领域的话题。我意识到我内心的语言学家在说话！

于是，我看了看我的书架，拿起我的那本 ATAP，开始了第 1 章。这是我阅读 ATAP 的第一篇文章，分享我的经验、想法和代码。

我对这个项目的意图是:

*   享受使用 Python 和人类语言数据的乐趣
*   提问和学习

更具体地说，我计划:

*   阅读书中的每一章
*   为每一章编写至少一个代码示例，并在 [Github](https://github.com/annawinkler) 上分享代码
*   每章至少写一篇文章，分享我的经验

我们开始吧！

# 什么是数据科学家？

本章介绍了数据科学，并将数据科学家定义为:

> 一部分是统计学家，一部分是计算机科学家，一部分是领域专家

因为数据是不可预测的，所以作者断言数据科学并不总是适合软件开发工作流(例如敏捷工作流)。本书希望通过描述直接支持应用程序开发的数据科学工具和过程来弥合这一差距。

根据作者的说法，应用文本分析:

> 支持创建“语言感知数据产品”

有意义的应用程序以有意义的、可理解的和现实的方式响应语言。

数据科学家创建描述语言的模型，并可用于进行预测。

# 语言特征和元素的例子

本章中的代码示例是英语中的性别模型。这个例子今天没有引起我的共鸣，所以我决定写一个元素(火、水、空气、土)的模型。对于这个例子，我用的是一个 [Jupyter](https://jupyter.readthedocs.io/en/latest/index.html) 笔记本，因为它非常简单有趣！我也用 PyCharm 作为我的编辑器。请随意使用您最喜欢的编辑器。这个[回购](https://github.com/annawinkler/elemental)有代码。

让我们首先定义不同元素的常数:

```
EARTH = 'earth'
AIR = 'air'
FIRE = 'fire'
WATER = 'water'
UNKNOWN = 'unknown'
```

现在，我们将手动创建模型。每个元素都有一个代表该元素的单词列表。因为我对这些元素的看法有偏差，可能你的模型会不一样，会做出不一样的预测。这个练习的目的是创建一个简单的模型，所以我们现在不用担心那些复杂性。我们也承认手工生成单词列表，包括添加复数和其他变体，效率不高。我们知道这一点—我们正在创建一个简单的模型，所以没问题！

```
*# I manually generated these lists by brainstorming, searching 
# for synonyms, and adding morphological variations of words 
# (e.g., adding plurals)* AIR_WORDS = {
    **'air'**, **'breeze'**, **'breezy'**, **'cloud'**, **'clouds'**, **'fog'**,
    **'gray'**, **'grey'**, **'light'**, **'mist'**, **'smoke'**, **'smog'**,
    **'white'**, **'wind'**, **'windy'** }

EARTH_WORDS = {
    **'brown'**, **'dirt'**, **'earth'**, **'earthy'**, **'green'**, **'ground'**,
    **'grounded'**, **'grounding'**, **'root'**, **'roots'**, **'soil'**, **'tree'**,
    **'trees'**, **'worm'**, **'worms'** }

FIRE_WORDS = {
    **'active'**, **'fire'**, **'flames'**, **'hot'**, **'roast'**, **'red'**,
    **'orange'**, **'yellow'**, **'sun'**, **'warm'**, **'warmth'** }

WATER_WORDS = {
    **'blue'**, **'downpour'**, **'drops'**, **'flow'**, **'moist'**, **'ocean'**,
    **'rain'**, **'river'**, **'sprinkle'**, **'stream'**, **'water'**, **'wave'**,
    **'waves'** }
```

现在，我们需要添加一个解析文本的方法。这个方法来自书中(对于这个特殊的例子有一个额外的 print 语句):

```
import nltk

def parse_element(text):
    sentences = [
        [word.lower() for word in nltk.word_tokenize(sentence)]
        for sentence in nltk.sent_tokenize(text)
    ]

    num_sentences, num_words = count_elements(sentences)
    total = sum(num_words.values())

    print(**f'Frequency-based score of how much of an Element (Air, Water, Fire, Earth) is in Text'**)
    for element, count in num_words.items():
        percent = (count / total) * 100
        nsents = num_sentences[element]
        print(**f'**{percent}**%** {element} **(**{nsents} **sentences)'**)
```

方法`parse_element`是我们的主要方法，它被我们想要分析的文本调用。在方法`parse_element`中，我们使用 [NLTK](https://www.nltk.org/) 库通过`sent_tokenize`找到文本中的句子，然后通过`word_tokenize`找到每个句子中的单词。

这个主方法调用如下所示的`count_elements`:

```
from collections import Counterdef count_elements(sentences):
    num_sentences = Counter()
    num_words = Counter()for sentence in sentences:
        element = elementalize(sentence)
        num_sentences[element] += 1
        num_words[element] += len(sentence)return num_sentences, num_words
```

这个方法计算句子的数量，每个句子的字数，并调用方法`elementalize`来获取给定句子的元素。

下面是方法`elementalize`:

```
def elementalize(words):
    earth_len = len(EARTH_WORDS.intersection(words))
    air_len = len(AIR_WORDS.intersection(words))
    water_len = len(WATER_WORDS.intersection(words))
    fire_len = len(FIRE_WORDS.intersection(words))
    element_counts = {EARTH: earth_len,
                      AIR: air_len,
                      WATER: water_len,
                      FIRE: fire_len}# If we don't find any element words, then we can't make any predictions.
    if earth_len == 0 and air_len == 0 and water_len == 0 and fire_len == 0:
        return UNKNOWN
    else:
        max_element_value = max(sorted(element_counts.values()))
        max_element_name = [k for k, v in element_counts.items() if v == max_element_value][0]
        return max_element_name
```

该方法计算单词列表中有多少个土、气、水和火单词，并返回最大数量。

对于这种方法，有一些事情是幼稚的:

*   如果元素计数相同，我们将只返回具有该计数的第一个元素。例如，如果地球和空气的计数都是 2，那么我们将返回列表中的第一个。
*   我们没有考虑多个元素匹配的情况。要么全有，要么全无，更多的粒度会给我们提供更多关于我们正在处理的文本的信息。

就是这样！下一个问题是我们从哪里得到文本？为了简单起见，我使用了一个随机段落生成器来生成一个段落。我用这个文本设置了一个局部变量，并用这个文本调用了`parse_element`,如下所示:

```
sample_text = 'The day had begun on a bright note. The sun finally peeked through the rain for the first time in a week, and the birds were singing in its warmth. There was no way to anticipate what was about to happen. It was a worst-case scenario and there was no way out of it.'parse_element(sample_text)
```

在 Jupyter 笔记本中运行这段代码，输出是:

```
Frequency-based score of how much of an Element (Air, Water, Fire, Earth) is in Text
59.32203389830508% unknown (3 sentences)
40.67796610169492% fire (1 sentences)
```

这告诉我们什么？好吧，对于这很小的一段随机文字，59%的文字是未知元素，40%是火。我们可以用这个模型做什么？我将在以后的帖子中讨论这个问题。我保证。

在我的下一篇文章中，我们将探索第 2 章，*构建自定义语料库*，我们将进一步扩展这个例子。那里见！