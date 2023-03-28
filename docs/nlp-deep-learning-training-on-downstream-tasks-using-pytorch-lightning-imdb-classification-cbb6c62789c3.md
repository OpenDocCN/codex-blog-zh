# 使用 Pytorch Lightning 对下游任务进行 NLP 深度学习培训— IMDB 分类—第 2 部分，共 7 部分

> 原文：<https://medium.com/codex/nlp-deep-learning-training-on-downstream-tasks-using-pytorch-lightning-imdb-classification-cbb6c62789c3?source=collection_archive---------12----------------------->

![](img/9f4ecacc9aa360dd72d8d96b84b63e7c.png)

这是第 2 部分，也是本系列的继续。请点击这里的[介绍文章](https://knswamy.medium.com/nlp-deep-learning-training-on-downstream-tasks-using-pytorch-lightning-intro-part-1-of-6-c338a05f86e6)了解这个系列的动机。正如简介中提到的，我们将查看 [IMDB 分类器 Colab 笔记本](https://github.com/kswamy15/NLP_Tasks_PyLightning/blob/main/Bert_NLP_Pytorch_IMDB_v4.ipynb)的各个部分，并对每个部分做出适当的评论。

1.  **下载并导入库**——这里没什么重要的。只需下载并导入常规的 Pytorch 和 Pytorch Lightning 库
2.  **下载数据**—IMDB 数据集经过预处理，可从变压器数据集库中下载。但是出于演示的目的，笔记本从公共存储库中下载数据，并以对培训有用的格式准备数据。该数据有 25000 个训练样本和 25000 个测试样本。
3.  **定义预训练模型** —这里使用的预训练模型是基于蒸馏的模型。它是 BERT 的精华版本，速度快 60%，内存轻 40%，但仍保留了 BERT 97%的性能。一旦您成功地完成了这方面的训练，就可以通过更改 model_checkpoint 变量来尝试其他预训练的模型。
4.  **定义预处理函数或数据集类** —这里我们定义 Pytorch 数据集继承类 IMDBDataset，它将创建数据加载器所需的数据集格式的训练、val 和测试数据。Pytorch 使用 DataLoader 类将数据构建成小批。在这个类中，使用预先训练的标记器对数据进行标记。
5.  **定义数据模块类** —这是一个 Pytorch Lightning 定义的类，包含使用数据加载器准备小批量数据所需的所有代码。在训练开始时，训练器类将首先调用*准备 _ 数据*和*设置*功能。在 *prepare_data* 功能中，根据以下事实为数据定义目标，即评论按照前 12，500 条属于正面评论，其余 12，500 条属于负面评论的顺序排列。训练数据被分成 75/25 训练和验证数据。这里有一个 *collate* 函数，用于填充小批量。Bert 类模型要求小批量的所有输入数据长度相同。collate 函数不是将输入数据填充到整个数据集的最大长度，而是帮助将小批量的输入数据填充到该小批量中最大长度的数据。这提供了更快的训练和更少的内存使用。
6.  **定义模型类**—DL 模型的正向函数在此定义。可以看出，来自最后一个隐藏层的输出(以及作为 CLS 令牌的输出的第一个元素的输出)取自 Bert 模型，并且在最终通过具有两个输出的线性层发送用于二进制分类之前，通过线性、Relu、丢弃层发送。CLS 令牌的输出被认为代表整个句子的意思。如果我们想从微调模型中为其他下游应用程序获取最后一个隐藏层的令牌嵌入，则定义 get_outputs 函数。

*   **定义 Pytorch Lightning 模块类别** —这是定义培训、验证和测试步骤功能的地方。在阶跃函数中计算模型损耗和精度。这里还定义了优化器和调度器——可以定义多个优化器和调度器，然后根据一些标准在阶跃函数中使用/切换。对于这个简单的例子，我们只使用一个 Adam 优化器和 OneCycleLR 调度器。
*   **定义训练器参数** —所有必需的训练器参数和训练器回调都在这里定义。我们定义了 3 种不同的回调——提前停止、学习速率监控和检查点。最新的 Pytorch Lightning 更新允许使用. yaml 文件定义参数，而不是使用 argparse 来定义参数。yaml 文件可以作为一个参数提供给 python。py 文件。这样，教练参数可以与训练代码分开维护。因为我们使用 Colab 笔记本进行演示，所以我们坚持使用 argparse 方法。
*   **训练模型** —这是使用 Trainer.fit()方法完成的。可以在训练器参数中定义一个分析器，以提供有关训练运行时间的更多信息。
*   **评估模型性能** —二元或多类分类是训练语言模型最容易的下游任务之一。您可以看到，在 IMDB 测试数据集上，仅一个历元之后，我们就获得了超过 92.6%的准确率——使用 Bert-base 模型而不是 DistilBert 模型将进一步提高准确率。IMDB 分类上的 [SOTA](https://paperswithcode.com/sota/sentiment-analysis-on-imdb) 在 2019 年达到了 97.4%，但 SOTA 使用了额外的训练数据。即使是准确率为 92.8%的 DistilBert 也使用了额外的训练数据。使用额外的训练数据，Bert-Large 获得了 95.49%的准确率。
*   **对训练好的模型运行推理** —使用 predict 方法向模型发送一个示例批处理文本，以从训练好的模型获得预测。这可以用于构建 ML 推理管道。
*   **TensorBoard 日志数据** —这将在 Colab 笔记本中打开 TensorBoard，让您查看各种 TensorBoard 日志。Pytorch Lightning 日志默认为 TensorBoard，这可以使用 Logger 回调来更改。

接下来，我们将在本系列的第 3 部分的[中了解令牌分类或命名实体识别(NER)任务培训。](https://knswamy.medium.com/nlp-deep-learning-training-on-downstream-tasks-using-pytorch-lightning-ner-on-conll-data-part-fe1512ae4183)