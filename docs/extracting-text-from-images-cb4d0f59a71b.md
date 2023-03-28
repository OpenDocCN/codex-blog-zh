# 从图像中提取文本

> 原文：<https://medium.com/codex/extracting-text-from-images-cb4d0f59a71b?source=collection_archive---------3----------------------->

![](img/8d1323657804ec989858bed67c22d5d4.png)

从这个博客的内容生成的 Wordcloud

文本识别是一种非常强大的机器学习技术，在基于文本的搜索和数字化方面有很大的潜力。我选择这个主题作为我在 Udacity 的机器学习工程师纳米学位项目的压轴项目。

有许多关于文本识别的博客文章(你可以很容易地从我链接的参考文献的数量中看出)！所以，你可能想知道这里有什么不同。我的目的是概述典型机器学习工作流程中的一些最重要的步骤——数据探索、模型选择和评估。这个博客是许多帖子的一个小汇编。我试图证明我对数据集和所选模型的观察，以理解建立一个能够识别任何文本的系统可能需要什么。

光学字符识别是涉及机器识别手写或机器书写文本的能力的更广泛的主题[1]。它有许多应用，如读取邮政地址、银行支票、申请表[2]、将遗产资料数字化为图像[3]、自动车牌识别[4]等等。这是人工智能研究的最早领域之一，也是当今相当成熟的技术[5]。事实上，今天有许多商业软件应用程序帮助文本数字化，例如，Tesseract、Adobe Acrobat Pro DC、Microsoft OneNote 等。[6].一种叫做智能单词识别的 OCR[7]能够解释不同风格和字体的手写文本，这是本文的主要焦点。

OCR 的应用最吸引我的是它在视觉障碍者和有阅读障碍的孩子的辅助技术中的应用。数字文本使得同时看到和听到单词成为可能，这对那些有阅读障碍的人来说非常有用。

我选择使用扩展的 MNIST 数据集[9]。这是一个转换成 28x28 像素图像的手写字符数据集。该数据集有 6 种不同的二进制和 CSV 格式。还有 6 种不同类型的拆分，我将使用从 Kaggle [10]下载的平衡拆分。这有 47 个类训练和测试数据。平衡分割具有更均匀的类别分布(每个类别约 2400 个图像)。

这是一个到我的 github 资源库的链接，其中包含一个 Jupyter 笔记本，上面有代码示例:

 [## madhu 90/机器学习项目

### 这个文件夹中的笔记本试验了扩展的 MNIST 数据库…

github.com](https://github.com/madhu90/MachineLearningProjects/tree/main/Character%20recognition) 

# ***数据探索***

![](img/3292a7f93c94225fb52afaab7006b24a.png)

让我们首先打印出训练和测试数据集的形状:

![](img/7a26c85a082a9a160d63146faaee5e3e.png)

图一。训练和测试数据的形状

我们有 112799 个训练样本和 18799 个测试点。第一列对应于标签，剩余的 784 列对应于 28×28 图像的实际像素强度。我们需要改变列的形状来理解数据的样子。

该数据集中使用了 47 个不同的类。最好为每个类选择一个例子，并将其形象化。

![](img/eec97d60a574f0a3a32da1d169e7e65d.png)

图二。每个类别中的示例训练图像

我们在所有标签上都有统一的训练样本分布(每个标签大约 2400 个)。

规范化是机器学习中的一个重要步骤。这确保了我们图像中的像素强度具有相似的数据分布[11]。因此，在这一步之后，我们的像素强度将位于 0 和 1 之间。

如果我们要为任何图像(假设字符“A”)绘制这些像素强度的直方图，它看起来会是这样的:

![](img/973621c85d158f2b94f0814aec00b1a2.png)

图三。样本训练图像的像素强度直方图

我们在这里看到的是，大多数像素强度为 0 或黑色，这对应于背景。实际图像的强度在 0 和 1 之间，这从图 3 中也很明显。

看看“A”级或“1”级的平均形象会是什么样子会很好[12]。这可以通过对给定类别的所有图像求和并除以该类别的样本总数来计算:

![](img/677a1096f252168abda78b0cf3b3b9c4.png)

图 4。所有类别的平均图像

当然有不同的方式来表示任何数字或字母——你可以改变字体，使用草书风格等等。图 4 告诉我们数据集的平均图像是什么样的，这似乎是非常标准的。

找出每个类别中的图像如何偏离平均图像(也称为质心)将是有趣的[12]。这告诉我们每个类别中有多少方差。我们可以使用欧几里德距离来计算两幅图像之间的距离:

![](img/dcf610a6ed45e2b9a9b8221d10dc31c1.png)![](img/9ae29e6597ddae1ec72a0b7976ef3d66.png)

图五。所有训练图像与其各自均值的距离的箱线图

方框的长度表示四分位数范围，并告诉我们数据的分散程度[13]。对于像“L”、“I”这样的类，方框长度相当长，这表明在表示该类的图像中一致性较低。例如，下面的图像是属于“我”的那些图像的例子。

![](img/fa42838b2ea23542ad1fe1d1a4c38ce0.png)

图六。位于由箱线图表示的各个四分位数的“I”类样本图像

在图 6 中间的图像中，我们看到有很多可变性，但离平均图像并不太远(右下角)。

图 6 中左边的图像示出了更接近平均值(Q1)的“I”的例子，而右边的图像示出了更远离平均值(> Q3)的例子，这从它们看起来是非常明显的。字母“I”的写法确实有很多种。

我们来看一个盒子长度小很多的例子——“Q”。

![](img/323ab6712a9f38fcd59261201b05fca7.png)

图 7。“Q”类的样本图像是由箱线图表示的各种四分位数

在类“Q”的例子中有较少的可变性。然而，大多数例子都与平均值相差甚远。这个字母表没有太多不同的表达方式。

让我们看 5 个不同类别的图像的子集，它们离各自的质心非常远。

![](img/80a191d39479e43912e66eea4274a43d.png)

图 8。a 类“H”、“X”和“7”的异常值

从图 8 中，很明显为什么这些图像离各自的质心有一段距离。

另一个有用的可视化是数字/字母的成对比较[12]。这生动地向我们展示了如何将一个阶级与另一个阶级区分开来。我们可以通过从所有其他类的质心中减去给定类的质心来绘制，如下所示。考虑下面的例子:

![](img/e0d26b2670feb0d651cfb429b0f2c857.png)

图九。“S”和“I”形心的成对比较

类别“I”和“S”彼此非常不同，这从差异图像中可以明显看出。我们可以很容易地将“S”识别为图像中的黑色区域。

# 型号选择

![](img/308f0665d10bc64a07641ba3b5e4d634.png)

进入典型机器学习工作流程的下一步。有许多机器学习模型可用于图像分类——SVM(支持向量机)、决策树、CNN(卷积神经网络)等[15]。当手动提取时，角、边缘、斑点、SURF 和 SIFT 等特征用于图像分类[16]。

细胞神经网络是最常用的图像分类模型，因为它们具有很高的精度。CNN 是前馈神经网络，并且它们在通过使用滤波器来减少要学习的参数数量方面是有效的。这些过滤器帮助我们利用图像的空间位置[17]。靠近的节点有助于识别特征，CNN 利用像素的空间相关性[18]。

卷积是一种数学运算，本质上是两个矩阵(一个图像和一个内核)按元素相乘，然后求和[19]，最终结果是模糊、平滑、边缘检测等，如下所示[20]。

![](img/5b091562d0ed2424a9a9b406f174b4de.png)

图 10。图像与不同核的卷积

内核定义了对图像执行什么操作。如果你堆叠不同类型的内核，你将能够从图像中提取不同的特征。简而言之，CNN 自动学习应该对图像应用什么内核来提取有意义的特征。

CNN 中有几种不同类型的层[21]:

a)卷积层-该层采用数学卷积的概念。过滤器或内核从上到下、从左到右在图像上移动，并且图像中的每个点与过滤器卷积以生成特征/激活图。该特征图是诸如边、角、线等特征的表示。

2)池层—此层的主要目的是减小要素地图的大小。要素地图记录要素在图像中的准确位置。这意味着特征在输入图像中的位置的微小变化将影响特征图[22]。我们需要确保特征检测过程是平移不变的。这就是池层发挥作用的地方。它通过仅输出来自网格[23]的最大值/平均值来降低特征地图的分辨率和复杂性。这样，关于最大值的精确位置的信息被丢弃。

3)完全连接的层——该层中的神经元与前一层中的所有激活都有完全连接[24]。来自先前层的输入被展平并馈送到该层。该层通常位于输出层之前。

输出层给出分类的结果，并且该层中的神经元的数量取决于数据集中的类的数量(47)。

我将使用 Keras Sequential [25]模型来实现 CNN，该模型的体系结构如下所示。

![](img/dd136acc19748a187f7cfd40d0b1c147.png)

图 11。在 EMNIST 数据集上使用的 CNN 模型

有两个卷积层，分别使用 32 和 64 个滤波器。有一个 128 个单位的完全连接层，并利用经校正的线性激活函数。靠近输入图像的过滤器会拾取小而精细的细节，而靠近输出的过滤器会捕获更一般的特征，如下图所示[26]:

![](img/43ae5efb91f31b1a8a95332dc0c27c98.png)

图 12。将所述第一卷积层应用于“8”类的训练图像而获得的激活图

![](img/dcf3483ca13b5599480f50dc4bc7d834.png)

图 13。通过将所述第二卷积层应用于“8”类的训练图像而获得的激活图

然后，我们将此模型与我们的培训数据进行拟合。我使用了 30%的培训示例进行交叉验证。我在训练集和验证集上绘制了模型的误差和准确度。对于每个历元，准确度都在提高，并且损失在减少。

![](img/b2fbb75cdc9f999412da12e3977fad4e.png)

图 14。每个训练周期的误差和准确度图

我们的模型对训练数据的准确率为 89.7%。

# **模型评价**

![](img/358a8586370c5e9e64e1b32e95dfe7b5.png)

现在是时候测试我们的模型了。将我们的模型应用于测试数据集，准确率为 87%。除了准确性之外，还可以考虑其他指标，如精确度、召回率和 F1 得分[27]。

![](img/9162dba74d6c5f8ef79b52388db94127.png)![](img/aa8a47614de4a219a462287f22b84652.png)![](img/2176fe66e60fc9baa92e3c9638b543b5.png)![](img/bdcd21ad82e5182d138f85363c275062.png)

图 15。我们的模型在每个类和整个测试数据中的精确度、召回率和 F1 分数

由于这是一个多类分类问题，因此使用宏平均来计算这些度量。它将多类预测问题简化为多组二进制预测，计算每个二进制情况的相应度量，然后将结果平均在一起[28]。

精确度、召回率和 F1 分数的加权宏观平均值分别为 0.88、0.87 和 0.87。如果你看一些像“L”，“f”，“q”这样的类，它们的回忆非常小。显然，我们的模型在这些类上表现不佳。让我们看看我们的模型的结果，并试图理解为什么会发生这种情况。

让我们首先来看看我们模型的错误分类率:

![](img/c9c63039cf098955b00bc4efb8942e35.png)

我们有一个均匀分布的测试数据(每个 400)，因此这个比率应该是可比的。当我为所有的类绘制这个图时，我得到的是:

![](img/99853aee6d719af5b2249cc75ef47b55.png)

图 16。测试数据集中所有类的错误分类率

类别 L、F、q、g、0 和 O 似乎具有最高的错误分类，并且这从低召回率中相当明显。让我们在这一类别中绘制一些测试示例，以可视化测试数据和预测标签。

![](img/b7bfb6abd0cd1e8bc17d18424213ae16.png)

图 17。被错误分类的样本图像及其相应的预测标签

我们预测错误的测试图像不是典型的例子，我们的模型似乎给出了合理的预测。

查看为经常混淆的类(例如 L 和 1 或 L 和 I)提供了什么样的训练数据也是一个好主意。下图显示了类别“L”的训练图像的子集，其位于 Q1 和 Q3 四分位数之间(相对于离类别质心的距离)。

![](img/7c725226d333748dd1f3a09379abca4c.png)

图 18。“L”类(左)和“I”类(右)的样本图像，其距各自质心的距离在 Q1 和 Q3 之间

我们看到的是，训练图像对区分这些标签没有太大帮助。从训练数据中过滤出这样的例子，或者用更有意义的合成例子来代替，这将是很好的。

查看与测试数据相关的标签也是一个好主意。从图 19 中，标记为“F”的图像看起来非常不正确。如果我们看更多被模型错误分类的“F”的例子，我们会看到:

![](img/f438bae7cf496302845996064901598a.png)

图 19。标有“F”的测试数据分类不正确。预测标签在每个图像的顶部

它们看起来非常像小写字母“f ”,我们的模型已经正确地将其归类为“f ”!这反过来可能是不良训练示例的结果:

![](img/44a3c74649a7906ba2983e27935cc4cb.png)

图 20。标记为“F”(左)和“F”(右)的训练数据

总的来说，有许多技术可以提高模型的准确性——数据扩充(当训练样本较少时)、剔除正则化(修复过度拟合)、增加训练数据大小或训练时期数量(修复欠拟合)、批量归一化(最小化训练时间)等等[29]。同样重要的是，要确保模型中使用的训练数据足够好，能够区分不同的类别。与测试数据相关的标签也应该是有效的。如果一个人不能准确地确定图像的类别，这很好地表明该图像不是训练的理想候选。

# 我们的模型概括得好吗？

由于我们的标签是字符和数字，我们可以轻松地生成合成数据并将其传递给我们的模型。我们可以创造性地使用不同的字体风格、形状和翻译效果来测试模型的功效。

![](img/3421194bcf8ec8870635325d5dc2e400.png)

图 21。合成数据(不同的字体风格和大小)来测试我们的模型

一旦你对模型运行新的数据，你观察到的是，像字体风格，大小，水平和垂直平移参数影响我们的模型的准确性。

![](img/f6b286cf8a4af88afbd194bc545aa5d9.png)

图 22。字体大小和样式对模型准确性的影响

这是意料之中的，因为我们还没有训练我们的模型使用这些字体样式和大小。因此，如果我们想让我们的模型识别任何字体样式和大小，我们必须在附加数据上训练它。

我还尝试用增强的训练数据来测试我们的模型。我挑选了一个错误分类率非常高的类(“F”)和一个错误分类率最低的类(“3”)。通过平均居中和执行不同的翻译来扩充数据。我为每个类生成了大约 100 张图片，并在模型中运行:

![](img/da0b34766873ace75b23a7e412834b82.png)

图 23。被增强的类别“F”的随机训练图像

对于增加的“F”，准确度是 0.48，而对于增加的“3”，准确度是 0.2。这是一个很好的迹象，表明测试和验证集上的模型精度不足以确保它能很好地概括。

简而言之，为了解决字符识别的更一般的问题，我们将需要采用数据扩充来获得更多的训练示例，在其他语言上训练模型，并且还识别和纠正标记偏差[30]。

**参考文献**

[1][https://www . science direct . com/topics/computer-science/optical-character-recognition](https://www.sciencedirect.com/topics/computer-science/optical-character-recognition)

[2][https://www . science direct . com/topics/social-sciences/character-recognition](https://www.sciencedirect.com/topics/social-sciences/character-recognition)

[3][https://www . digital NC . org/blog/digital-collections-ocr-what-it-is-and-what-it-nots/](https://www.digitalnc.org/blog/digital-collections-ocr-what-it-is-and-what-it-isnt/)

[https://en.wikipedia.org/wiki/Optical_character_recognition](https://en.wikipedia.org/wiki/Optical_character_recognition)

[https://research.aimultiple.com/ocr-technology/](https://research.aimultiple.com/ocr-technology/)

[6][https://beebom.com/best-ocr-software/](https://beebom.com/best-ocr-software/)

[7][https://en . Wikipedia . org/wiki/Intelligent _ character _ recognition](https://en.wikipedia.org/wiki/Intelligent_character_recognition)

[8][https://www . understand . org/en/school-learning/assistive-technology/assistive-technologies-basics/how-does-optical-character-recognition-help-kids-with-reading-issues](https://www.understood.org/en/school-learning/assistive-technology/assistive-technologies-basics/how-does-optical-character-recognition-help-kids-with-reading-issues)

[9][https://www . NIST . gov/ITL/products-and-services/em NIST-dataset](https://www.nist.gov/itl/products-and-services/emnist-dataset)

[https://www.kaggle.com/crawford/emnist](https://www.kaggle.com/crawford/emnist)

[11][https://becoming human . ai/image-data-pre-processing-for-neural-networks-498289068258](https://becominghuman.ai/image-data-pre-processing-for-neural-networks-498289068258)

[http://varianceexplained.org/r/digit-eda/](http://varianceexplained.org/r/digit-eda/)

[13][https://www . thoughtco . com/what-the-inter quartile-range-3126245](https://www.thoughtco.com/what-is-the-interquartile-range-3126245)

[14][https://towards data science . com/explaining-k-means-clustering-5298 DC 47 bad 6](https://towardsdatascience.com/explaining-k-means-clustering-5298dc47bad6)

[15][https://IQ . open genus . org/basics-of-machine-learning-image-class ification-techniques/](https://iq.opengenus.org/basics-of-machine-learning-image-classification-techniques/)

[16][https://www . rsip vision . com/image-features-for-class ification/#:~:text = Well % 20 known % 20 examples % 20 of % 20 image，features % 20 can % 20 be % 20 wisely % 20 selected](https://www.rsipvision.com/image-features-for-classification/#:~:text=Well%20known%20examples%20of%20image,features%20can%20be%20wisely%20selected)

[17][https://www . analyticsvidhya . com/blog/2021/01/image-class ification-using-convolutionary-neural-networks-a-step-by-step-guide/](https://www.analyticsvidhya.com/blog/2021/01/image-classification-using-convolutional-neural-networks-a-step-by-step-guide/)

[18][https://towards data science . com/simple-introduction-to-convolutionary-neural-networks-cdf8d 3077 BAC #:~:text = There % 20 are % 20 three % 20 types % 20 of，Features % 20 of % 20a % 20 convolutionary % 20 layer](https://towardsdatascience.com/simple-introduction-to-convolutional-neural-networks-cdf8d3077bac#:~:text=There%20are%20three%20types%20of,Features%20of%20a%20convolutional%20layer)

[19][https://www . pyimagesearch . com/2016/07/25/convolutions-with-opencv-and-python/](https://www.pyimagesearch.com/2016/07/25/convolutions-with-opencv-and-python/)

[20][https://medium . com/analytics-vid hya/2d-convolution-using-python-numpy-43442 ff5f 381](/analytics-vidhya/2d-convolution-using-python-numpy-43442ff5f381)

[21][https://www . upgrad . com/blog/basic-CNN-architecture/#:~:text = There % 20 are % 20 three % 20 types % 20 of，CNN % 20 architecture % 20 will % 20 be % 20 formed](https://www.upgrad.com/blog/basic-cnn-architecture/#:~:text=There%20are%20three%20types%20of,CNN%20architecture%20will%20be%20formed)

[22][https://machine learning mastery . com/pooling-layers-for-convolutionary-neural-networks/](https://machinelearningmastery.com/pooling-layers-for-convolutional-neural-networks/)

[23][https://div soni 2012 . medium . com/translation-invariant-in-convolutionary-neural-networks-61 d9 b 6 fa 03 df](https://divsoni2012.medium.com/translation-invariance-in-convolutional-neural-networks-61d9b6fa03df)

[https://cs231n.github.io/convolutional-networks/#fc](https://cs231n.github.io/convolutional-networks/#fc)

[https://keras.io/api/models/sequential/](https://keras.io/api/models/sequential/)

[26][https://machine learning mastery . com/how-to-visualize-filters-and-feature-maps-in-convolutionary-neural-networks/](https://machinelearningmastery.com/how-to-visualize-filters-and-feature-maps-in-convolutional-neural-networks/)

[27][https://towards data science . com/accuracy-precision-recall-or-f1-331 FB 37 C5 CB 9](https://towardsdatascience.com/accuracy-precision-recall-or-f1-331fb37c5cb9)

[28][https://cran . r-project . org/web/packages/码尺/vignettes/multiclass.html](https://cran.r-project.org/web/packages/yardstick/vignettes/multiclass.html)

[29][https://www . analyticsvidhya . com/blog/2019/11/4-tricks-improve-deep-learning-model-performance/](https://www.analyticsvidhya.com/blog/2019/11/4-tricks-improve-deep-learning-model-performance/)

[30][https://towards data science . com/identifying-and-correcting-label-bias-in-machine-learning-ed 177d 30349 e](https://towardsdatascience.com/identifying-and-correcting-label-bias-in-machine-learning-ed177d30349e)