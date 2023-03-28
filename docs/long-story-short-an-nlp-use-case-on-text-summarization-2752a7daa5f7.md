# 长话短说..文本摘要的自然语言处理用例

> 原文：<https://medium.com/codex/long-story-short-an-nlp-use-case-on-text-summarization-2752a7daa5f7?source=collection_archive---------5----------------------->

![](img/ae6332da9ff418df8afd8923eb294c89.png)

在这个快节奏的时代，我们都想随时了解全球每天发生的事情，但很少有人有这种奢侈。至于阅读报纸和文章，略读是要点，即先浏览标题，然后只有当它有趣或值得阅读时才跳进去。特别是当点击一个按钮就可以看到内容时，我们都更喜欢浏览从流行报纸、网站、博客和杂志上收集的“摘要”新闻；用三到四句话来表达——而不是阅读冗长的、有时被高估和夸大的报纸文章。

> 人工智能领域中的文本摘要是一个重要的研究领域，它从一段文本中识别出相关的句子。通过文本摘要，我们可以通过保留文本的内容来获得简短而精确的信息。

谷歌新闻、Inshorts、Pulse 是一些利用文本摘要算法的新闻聚合应用。在这篇文章中，我将带你通过各种传统和先进的方法来实现自动文本摘要

文本摘要大致分为两类——提取摘要和抽象摘要。

*   **摘录摘要** —这是一种传统方法，根据句子的重要性直接从原始文档中提取句子。请注意，获得的摘要包含原文中的精确句子。
*   **抽象概括——**抽象概括更接近于人类通常所做的事情——即构思文本，将其与他/她的记忆和相关信息进行比较，然后在简短的文本中重新创建其核心。这就是为什么抽象摘要比抽取方法更具挑战性的原因，因为模型应该将源语料库分解成非常多的标记，并重新生成目标句子。在摘要中实现有意义和语法正确的句子是一件大事，需要高度精确和复杂的模型。

在接下来的部分中，让我们探索提取和抽象方法，您可以比较和熟悉每种方法的优点和局限性。

考虑一下来自商业新闻网站的特斯拉季度业绩摘录

```
text = """
Tesla reported second-quarter earnings after the bell Monday, and it’s a beat on both the top and bottom lines. Shares rose about 2% after-hours. Here are the results.Earnings: $1.45 vs 98 cents per share adjusted expected, according to Refinitiv. Revenue: $11.96 billion vs $11.30 billion expected, according to RefinitivTesla reported $1.14 billion in (GAAP) net income for the quarter, the first time it has surpassed $1 billion. In the year-ago quarter, net income amounted to $104 million.Overall automotive revenue came in at $10.21 billion, of which only $354 million, about 3.5%, came from sales of regulatory credits. That’s a lower number for credits than in any of the previous four quarters. Automotive gross margins were 28.4%, higher than in any of the last four quarters.Tesla had already reported deliveries (its closest approximation to sales) of 201,250 electric vehicles, and production of 206,421 total vehicles, during the quarter ended June 30, 2021.The company also reported $801 million in revenue from its energy business, including solar photovoltaics and energy storage systems for homes, businesses and utilities, an increase of more than 60% from last quarter. While Tesla does not disclose how many energy storage units it sells each quarter, in recent weeks CEO Elon Musk said, in court, that the company would only be able to produce 30,000 to 35,000 at best during the current quarter, blaming the lag on chip shortages. Tesla also reported $951 million in services and other revenues. The company now operates 598 stores and service centers, and a mobile service fleet including 1,091 vehicles, an increase of just 34% versus a year ago. That compares with an increase of 121% in vehicle deliveries year over year. A $23 million impairment related to the value of its bitcoin holdings was reported as an operating expense under “Restructuring and other.”
"""
```

让我们从使用不同方法安装和导入运行总结过程所需的库开始。

```
!pip install bert-extractive-summarizer
!pip install spacy
!pip install transformers
!pip install torch
!pip install sentencepiece
```

导入库

```
import gensimfrom gensim.summarization import summarizeimport torchfrom transformers import pipeline
from transformers import T5Tokenizer
from transformers import T5ForConditionalGeneration
from transformers import T5Configfrom summarizer import Summarizer
from summarizer import TransformerSummarizer
```

## 使用 Gensim(文本等级)的摘要

gensim 包用于自然语言处理和信息检索任务，如主题建模、文档索引、word2vec 和相似性检索。这里我们使用 TextRank 算法将它用于文本摘要。

TextRank 是一种提取的和无监督的文本摘要技术，基于更频繁出现的单词是有意义的概念。因此，包含高频词的句子很重要。基于此，算法给文本中的每个句子分配分数。排名靠前的句子会进入摘要。

让我们使用下面给出的不同汇总参数来使用 gensim 的`summarize`功能

1.  `ratio`:取值范围为 0-1。它表示摘要相对于原始文本的比例。
2.  `word_count`:决定摘要的字数。

**按比例汇总:**

```
summary_by_ratio = summarize(text, ratio=0.15) 
print("Summary : \n" + summary_by_ratio)**Output >>>**Summary :  The company also reported $801 million in revenue from its energy business, including solar photovoltaics and energy storage systems for homes, businesses and utilities, an increase of more than 60% from last quarter. Tesla also reported $951 million in services and other revenues.
```

**按字数总结**

```
summary_by_count=summarize(text, word_count=60)
print("Summary : " + summary_by_count)**Output >>>**Summary : Overall automotive revenue came in at $10.21 billion, of which only $354 million, about 3.5%, came from sales of regulatory credits. The company also reported $801 million in revenue from its energy business, including solar photovoltaics and energy storage systems for homes, businesses and utilities, an increase of more than 60% from last quarter. Tesla also reported $951 million in services and other revenues.
```

# 使用预先训练的模型进行抽象概括

这里我们使用 Python 中的 transformers 库对输入文本执行抽象文本摘要。变形金刚库的所有文档都可以在这个网站上找到:[https://huggingface.co/transformers/](https://huggingface.co/transformers/)

现在让我们用下面的方法来研究总结技术。

*   管道 API
*   T5 变压器
*   伯特
*   GPT2
*   XLNet

# 使用管道 API 汇总

在 transformers 中使用模型最直接的方式是使用管道 API。管道是使用模型进行推理的一种伟大而简单的方式。这些管道是从库中抽象出大多数复杂代码的对象，提供了一个专用于多项任务的简单 API，包括命名实体识别、屏蔽语言建模、情感分析、特征提取和问题回答

此管道可以使用的模型是已经针对摘要任务进行了微调的模型，当前为“bart-large-cnn”、“t5-small”、“t5-base”、“t5-large”、“t5–3b”、“t5–11b”

使用默认模型运行汇总管道

```
summarization = pipeline("summarization")## Call the transformer's summarization api by passing textabstract_text = summarization(text)[0]['summary_text']
print("Summary:", abstract_text)
```

注意:当您第一次执行这个或者上面列出的任何一个技术时，下载模型架构和权重，以及在某些情况下的记号化器配置需要一些时间。

下面是执行上述`pipeline`代码后生成的摘要

```
**Output >>>**Summary:  Tesla reported $1.14 billion in (GAAP) net income for the quarter, the first time it has surpassed $1 billion . The company also reported $801 million in revenue from its energy business, including solar photovoltaics and energy storage systems . Shares rose about 2% after-hours .
```

生成的抽象摘要看起来相当不错，不是吗！

通过显式传递`t5-base`模型来运行相同的管道

```
## setup the pipeline with t5-base model
t5summarizer = pipeline("summarization", model="t5-base", tokenizer="t5-base", framework="tf")## Build the summary with min 5 and max 60 words
t5summarizer(text, min_length=5, max_length=60)**Output >>>**Summary: Tesla reported $1.45 vs 98 cents per share adjusted expected, according to Refinitiv . overall automotive revenue came in at $10.21 billion, of which only $354 million, about 3.5%, came from sales of regulatory credits
```

# T5 变压器概述

T5 是在非监督和监督任务的多任务混合上预先训练的编码器-解码器模型，并且对于该模型，每个任务被转换成文本到文本格式。T5 通过在对应于每个任务的输入前添加不同的前缀，可以很好地处理各种现成的任务

通过`from_pretrained`方法实例化预训练的“t5-small”模型。

```
t5model = T5ForConditionalGeneration.from_pretrained('t5-small')t5tokenizer = T5Tokenizer.from_pretrained('t5-small')device = torch.device('cpu')
```

在原始文本的开头添加字符串`summarize:` 并编码。T5 转换器通过在输入文本前添加特定的前缀来执行不同的任务。

```
t5tokenized_text = t5tokenizer.encode("summarize:"+ text,
                                truncation=True,
                                return_attention_mask=True,
                                add_special_tokens=True, 
                                padding='max_length',     
                                return_tensors="pt").to(device)
```

接下来，您将传递 encode 函数返回的标记化文本中的`input_ids`以及其他参数。这里使用的技术是“波束搜索”。波束搜索通过在每个时间步长保持最可能的假设波束数，并最终选择具有总体最高概率的假设，降低了丢失隐藏的高概率单词序列的风险

在这里阅读更多关于贪婪搜索和光束搜索:[https://huggingface.co/blog/how-to-generate](https://huggingface.co/blog/how-to-generate)

```
t5summary_ids =  t5model.generate(input_ids=t5tokenized_text['input_ids'],
                 attention_mask=t5tokenized_text['attention_mask'],
                 num_beams=3,
                 min_length=20,
                 max_length=70,
                 repetition_penalty=2.0,
                 early_stopping=True)
```

参数解释如下:

*   `max_length`:生成令牌的最大数量。
*   `min_length`:这是要生成的最小令牌数。
*   `length_penalty`:对长度的指数惩罚，1.0 表示没有惩罚，增加该参数，将增加输出文本的长度。
*   `num_beams`:指定该参数，将引导模型使用波束搜索而不是贪婪搜索，将 num_beams 设置为 4，将允许模型前瞻 4 个可能的单词(贪婪搜索时为 1)。
*   `early_stopping`:我们将它设置为 True，这样当所有的 beam 假设到达字符串标记的末尾(EOS)时，生成就完成了。

```
output = t5tokenizer.decode(t5summary_ids[0],  
                             skip_special_tokens=True, 
                             clean_up_tokenization_spaces=True)print ("Summary:", output)**Output >>>**Summary: shares rose about 2% after-hours, according to Refinitiv. in the year-ago quarter, net income amounted to $104 million. overall revenue came in at $10.21 billion, of which only $354 million came from regulatory credits.
```

# 用 BERT 模型总结

**BERT** (双向转换器)是一种转换器，用于克服 RNN 和其他神经网络作为长期依赖的局限性。这是一个预先训练好的模型，自然是双向的。这个预先训练好的模型可以被调整以容易地执行指定的 NLP 任务，在我们的例子中是**总结**。

```
bert_model = Summarizer()bert_summary = ''.join(bert_model(text, min_length=60))print("Summary: " + bert_summary)**Output >>>**Summary: Tesla reported second-quarter earnings after the bell Monday, and it’s a beat on both the top and bottom lines. The company also reported $801 million in revenue from its energy business, including solar photovoltaics and energy storage systems for homes, businesses and utilities, an increase of more than 60% from last quarter. That compares with an increase of 121% in vehicle deliveries year over year.
```

# 用 GPT2 模型总结

OpenAI 的生成式预训练转换器(GPT)模型通过引入非常强大的语言模型，在自然语言处理(NLP)社区掀起了风暴。这些模型可以执行各种 NLP 任务，如[问题回答](https://en.wikipedia.org/wiki/Question_answering)、[文本蕴涵](https://en.wikipedia.org/wiki/Textual_entailment#:~:text=Textual%20entailment%20(TE)%20in%20natural,hypothesis%20(h)%2C%20respectively.)、文本摘要等。没有任何监督训练。

GPT-2 能够在更大和更复杂的规模上预测下一个单词。作为参考，最小的 GPT-2 有 1.17 亿个参数，而最大的(公众不可见)有超过 15 亿个参数。可供公众使用的最大的一个是他们主要的 GPT-2 模型的一半大小。

让我们加载模型，并获得生成的摘要！

```
GPT2_model = TransformerSummarizer(transformer_type="GPT2",
                         transformer_model_key="gpt2-medium")gpt_summary = ''.join(GPT2_model(text, min_length=60))print(gpt_summary)**Output >>>**Summary: Tesla reported second-quarter earnings after the bell Monday, and it’s a beat on both the top and bottom lines. In the year-ago quarter, net income amounted to $104 million. The company also reported $801 million in revenue from its energy business, including solar photovoltaics and energy storage systems for homes, businesses and utilities, an increase of more than 60% from last quarter.
```

哇！比伯特好太多了！看看这样一个**有见地的总结，它已经产生！**

现在，让我们进入最后一个模型，**XLNet！**

# 使用 XLNet 总结

XLNet 是一种广义自回归语言模型，它学习文本序列的无监督表示。该模型将来自自动编码器(AE)模型(BERT)的建模技术结合到 AR 模型中，同时避免了 AE 的局限性。

让我们加载模型并打印出摘要/

```
xlnet_model = TransformerSummarizer(transformer_type="XLNet",
                     transformer_model_key="xlnet-base-cased")xlnet_summary = ''.join(xlnet_model(text, min_length=60))print("Summary: " + xlnet_summary)**Output >>>**Summary: Tesla reported second-quarter earnings after the bell Monday, and it’s a beat on both the top and bottom lines. That’s a lower number for credits than in any of the previous four quarters. The company also reported $801 million in revenue from its energy business, including solar photovoltaics and energy storage systems for homes, businesses and utilities, an increase of more than 60% from last quarter.
```

对于所有的 transformer 模型，生成的结果说明它工作得非常好，这真是令人印象深刻！👌

# 结论

在当今快速发展的信息时代，文本摘要已经成为辅助和解释文本信息的重要和及时的工具。无论在什么领域，只要涉及文本文档，就可以看到它的应用。由于文本摘要的应用及其带来的便利性，文本摘要是自然语言处理领域的一个热门话题。

我希望这篇文章能让你对文本摘要的不同类型和不同方法有所了解。如果是，请分享掌声..👏