# 人工智能算法交易

> 原文：<https://medium.com/codex/algorithmic-trading-with-artificial-intelligence-5e82e5b2c1a3?source=collection_archive---------1----------------------->

## 捍卫美元

![](img/bf503d0bf8b28d63897c16afe6b60862.png)

弗拉基米尔·库什的《千年守望者》图片

# **简介**

大约有 3，500 家上市公司可以在美国股市交易，但 80%以上的每日价格波动都是通过算法实现的。金融交易已经从传统的大脑驱动的理性主义演变为数据驱动的经验主义。下面，我将对选择美元进行人工智能算法交易进行彻底的论证，因为美元的储备货币被广泛误解。让我们首先从操作定义开始。

![](img/c34fa227dac52f0a82fd7d7ce40b9735.png)

图片作者:约翰·福克斯沃西

# **货币市场领域知识**

货币已经存在了几千年，但在今天它以不同的方式表达，有着复杂的因素。美元是全球储备货币，也是全球无数商业关系的基础抵押品，更不用说数十种金融市场产品了。由于多重影响，很难确定推动美元的因素。像线性回归这样的单层方程的单次验证可能由于状态转换而改变。换句话说，**因果关系来得突然，去得也突然，没有任何警告和可能的随机性**。几十个国家的进口、出口、政府预算和当地通胀率等等，都会增加或减少它们对美元的影响。我们离掌握美元全球供应及其全球需求的数据工程还差得很远。

更重要的是，一种货币是如何崩溃的？我很惊讶世界上有这么多的人相信一种货币会因为接近零值而崩溃。历史上所有过去的货币崩溃都有一个货币汇率值，离零不远，但非常远。举几个例子，比如二战前魏玛共和国的德国马克，或者 10 年前委内瑞拉玻利瓦尔或津巴布韦元的通货膨胀。

![](img/8f768dccba4d5ed48c1139a2b99ac45b.png)

维基百科图片

![](img/cbb3f7a4e2a22001328348b99b862066.png)

维基百科图片

公司不是主权实体，因为股票是公司发行的，货币是政府发行的。在我们建模之前，我们必须了解价值的方向，因为没有价值的资产没有需求，也没有价值接近于零的供给。然而，一种没有价值的货币需求很少，供应大量过剩，其价值根本不会接近零。具体来说，货币不是资产，因为它是一种交换。 **一项资产的价值并不等同于一项交换的价值。**就像室内温度计是房子的资产，因为它可以读取房子的温度，但这与房子里的居民如何交换温度值或意见*无关*。单个产品上的单个数据点与同一产品上的多个数据点*不*相同。价值观可能会趋同，但更重要的是，价值观几乎总是会有分歧。

# **数据科学建模**

从数据科学的角度来看，美元在监督学习中建模是令人生畏的。**具有线性回归等特征选择的经典模型或改进的当代版本(如 Ridge、Lasso、Elastic Net、Support Vector 或 Random Forest)可以捕捉美元的波动，但只是暂时的。**一周你有利润，下一周你就不知道你的收入发生了什么。其他没有特征选择和趋势跟踪的单层模型是相同的，例如经典的 Box-Jenkins，更通常被称为 ARIMA，以及当代的脸书先知模型。最后，超越经典的数据、模型和模型迭代输出三步序列的当代模型很有前途，如极端梯度推进或 XGBoost。总之，我们需要一个模型，它不是静态的，有令人沮丧的固定数量的因素驱动美元价值，对美元需求和供应的随机性和制度转换开放。

[](https://foxworthy-8036.medium.com/18-types-of-predictive-models-in-data-science-b53275810032) [## 数据科学中的 18 种预测模型

### 尝试对监督学习模型进行分类(1.0)

foxworthy-8036.medium.com](https://foxworthy-8036.medium.com/18-types-of-predictive-models-in-data-science-b53275810032) ![](img/93d33801b4add9e1ffd8a8011452ba2f.png)

匿名图片

# **美元的描述性分析、简单的无监督学习和更多领域知识**

为了提高我们对美元的认识，我们将使用**探索性数据分析**和相关矩阵来描述具有共同主题的货币对之间的关系。下图中，深蓝颜色像澳元、加元和欧元一样呈串联走势。。。较浅的颜色与英镑和日元的走势相反。

![](img/74026c3a19bef09b595a090829980620.png)

图片由 John T Foxworthy 提供(*采用倒数是因为这些货币对不以美元报价)

请注意日元的一个问题，日元是其他东亚经济体，尤其是中国的再分配中心。中国经济对美元的需求影响着美元对日元的汇率。最重要的是，自 2005 年以来，人民币对美元实行有管理的固定汇率政策，这要求人民币在向美国和世界其它地区出口更多的同时，买入更多美元。中国经济在出口时没有将人民币重新分配到世界其他地区，因为其他国家接受美元作为结算货币。中国发展得越快，它向世界其他地方传播的美元就越多。

![](img/00fe3bac723a86661bda03f6530d9dc0.png)

图片由迈克尔·纽勒拍摄

几十年的全球化可以在货币市场上总结为以下几件事。**英镑和日元都是中东、非洲和亚洲国家重要出口的特殊用途货币。**外汇市场类似于由大约 10 种货币进行大部分交易的寡头垄断，尽管全球有大约 200 个国家。许多货币(并非全部)将其本地资产与英镑和日元挂钩，而不是与疲软且不可靠的本地货币挂钩。(全球大多数地方货币都将其本地资产与美元挂钩。)与美元成反比的总体原因与出口商品和服务有关。这些国家出口越多，其货币升值越多。。。因此，为了阻止当地的通货膨胀，你接受美元汇率。**对你的商品和服务的需求随着本币的升值而上升。。。由于全球贸易，全球对美元的需求基本上是永久性的。**为什么几十个国家，也就是十亿左右的工人会停止出口他们的商品和服务，从而集体失业？美元负相关的主要驱动力是出口成功的水平。

![](img/9f248e7c18d4902560ebbf6223048d9f.png)

图片作者:约翰·福克斯沃西

切换到另一个话题，下面是对过去衰退日期的关注，因为上述论点随着经济衰退而增强。

![](img/2725a26746258e5099dd283bfec97ae1.png)

图片作者:约翰·福克斯沃西

![](img/93aeb61cb25028f69f9edc196e176cba.png)

图片由 John T Foxworthy 提供(*采用倒数是因为这些货币对不以美元报价)

美国央行或美联储已经标记了过去的衰退日期，重新运行相关性矩阵显示，与美元的较浅颜色的背离转变为较深颜色的趋同。英镑和日元是例外，但正相关性从 0.039 增加到 0.25，或者说增加了五倍，趋于一致。此外，有 3 个聚类通过肘方法识别，如果您选择简单的 K-Means 聚类算法和平面几何数学，那么您会得到类似的结果。

![](img/b67a30a118d6694396b38badd04a1a86.png)

图片作者:约翰·福克斯沃西

![](img/caebb3dd71a62fdba7215784986e9b51.png)

图片作者:约翰·福克斯沃西

我们可以得出结论，作为储备货币的美元在衰退期间作为避险货币相对于其他货币升值，这是因为串联相关性和集群密度的松动。每种货币就像一辆汽车，在高速公路上各就各位，直到出现经济衰退，所有货币都朝着美元的方向单向行驶。美国过去的所有衰退都是美元对其他货币以势不可挡的方式持续升值。

# **美元崩溃的错觉**

有很多关于 2021 年美国股市可能崩盘的言论，错误地预期美元将变得一文不值。相反，**有大量证据表明，公司、政府和家庭的美元计价债务数量是有史以来最大的。在经济萧条时期，偿还所有这些债务成为一个显而易见的优先事项，从而产生对美元的强劲需求。不会就用美元偿还债务以换取黄金或加密货币进行重新谈判，因为我们没有这样做的法律机制。更不用说会改变价值、增加不必要风险的第三种额外货币了。(认为第三种货币可行的人混淆了货币和资产，或者交换价值和资产价值。).最重要的是，为什么会有不计其数的贷款人集体承诺重新谈判美元债务？在全球经济低迷时期，美元需求总是会增加，因为美元是无数商业交易的基础抵押品。**

![](img/4c3c2f07ab32990143a59f33d3195a3e.png)

弗拉迪米尔·库什的突破图像

# **销毁加密货币**

在我进入 AI 之前，让我快速摧毁加密货币作为美元货币的替代品。。。

(1)不是记账单位，因为没有任何东西在其中定价。

(2)不是可扩展的支付手段。与 24 小时内有数百万笔交易的美元相比，加密货币的交易量只有半打。你不可能在一天内完成一百万笔加密货币交易。

(3)相对于商品和服务，没有稳定的价值储存，因为有太多的波动性。如果你今天与海外合作伙伴使用加密货币，但交易在第二天结算，那么你就会面临巨大的价格波动。加密货币不能代替出口和进口的融资，因为它具有很强的不稳定性。

(4)加密货币不是货币，因为它是资产。更有甚者，**资产通常会给你收益或者现金流，比如股票给你分红，债券给你息票，贷款给你利息，房产给你租金，但是加密货币的收益是什么呢？黄金也没有收益。**

(5)即使上述情况都不成立，所需电量也是不可行的。如果比特币是一个国家的话，剑桥大学的比特币电力消费指数今年在 196 个国家中排名第 29 位。**如果加密货币取代美元，将需要人类历史上最大的电量！**你无法在物理上实现比特币取代美元。

# 人工智能算法交易

![](img/2fe3242c1e6f601054334871b32444fb.png)

Storm Thorgerson 拍摄的树头图像

全权投资组合经理可以用其生物智能将公司股票作为资产进行评估，但货币的复杂性需要人工智能。获得诺贝尔奖的经济学家尤金·法玛(Eugene Fama)可以用他出色的线性确定性方法解释股票资产，这种方法被称为三因素法玛-弗伦奇模型。具有递归、卷积和其他方法的深度学习模型捕捉了汇率的随机性、制度转换以及可见或不可见的特征。数量不固定且随时间变化的一组特征可以被描述为永久的、临时的、暂时的、随机的等等。模型中的所有特征都将增加人工智能的非生物智能的价值。然而，生物智能可能无法在这些类型的模型中单独识别这些特征。您可能会使用上一节中关于数据科学建模的内容来演示和证明某个特定功能有助于美元预测。

人工神经网络确实需要大量的数据工程、数据预处理和最重要的模型训练。从输入层到输出层的前向传播和反向传播为金融交易的生产建立了一组额外指标的模型。最重要的是对每个数据序列进行无缝验证的回溯测试。

![](img/cf1a34087c4f3b6d8b9a41600f77dbfd.png)![](img/528d1994217a9c86103ca9857ad41c56.png)

图片作者:约翰·福克斯沃西

我想展示更多，但我会就此打住。你可能会喜欢我最近写的关于**美国收益率曲线**和美国债券**期权调整利差**的文章，这两篇文章都有评论和 Python 编程。如果你有任何问题，请联系我。

[](https://foxworthy-8036.medium.com/the-most-important-business-object-in-the-debt-markets-64e6b796579b) [## 债务市场中最重要的业务对象

### 用 Python 编程制作收益率曲线

foxworthy-8036.medium.com](https://foxworthy-8036.medium.com/the-most-important-business-object-in-the-debt-markets-64e6b796579b) [](https://foxworthy-8036.medium.com/how-to-calculate-option-adjusted-spread-oas-for-bonds-with-python-programming-a7176944d325) [## 如何用 Python 编程计算债券的期权调整利差(OAS)

### 债券很复杂，尤其是非传统债券。固定收益行业最受欢迎的指标之一…

foxworthy-8036.medium.com](https://foxworthy-8036.medium.com/how-to-calculate-option-adjusted-spread-oas-for-bonds-with-python-programming-a7176944d325) 

[https://www . LinkedIn . com/in/John-t-fox worthy-ms-data-science-1718073/](https://www.linkedin.com/in/john-t-foxworthy-ms-data-science-1718073/)