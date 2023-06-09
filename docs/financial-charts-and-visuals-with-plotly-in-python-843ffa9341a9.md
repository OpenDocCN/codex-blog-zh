# Python 中的财务图表和视觉效果

> 原文：<https://medium.com/codex/financial-charts-and-visuals-with-plotly-in-python-843ffa9341a9?source=collection_archive---------0----------------------->

![](img/31594f2524a0cfce82cbd5ca9f16d4fc.png)

## [法典](http://medium.com/codex)

## 用 Python 中的 Plotly 实现代码更少的专业金融图表

**免责声明:**本文纯属教育目的，不应被视为投资建议。

# 可视化的 Plotly

用于交互式可视化的最流行的软件包之一是 Plotly。Plotly 建立在 python 之上，使数据科学家能够用更少的代码生成专业且美观的图形。它之所以受欢迎，是因为它的情节种类繁多，可以随时制作。图的类别包括基本图表、统计图表、ML 和 AI 图表、科学图表和金融图表。

在这篇文章中，我将带您了解用 python 在 Plotly 中创建交互式专业财务图表的过程。我们还将探索 yahoo 的 API 来提取历史股票数据，我们将使用这些数据进行可视化。我们开始吧！

# 导入包

我们的主要软件包包括用于数据处理的 pandas，用于提取历史股票数据的 pandas DataReader，用于处理日期的 Datetime，最后是用于交互式可视化的 Plotly 及其依赖项。按照代码将主要包导入到我们的 python 环境中。

**Python 实现:**

我们的下一个过程将是使用 pandas DataReader 包提取可视化的历史股票数据。

# 提取股票数据

对于我们的可视化，我们将使用雅虎的 API 提取六家公司的历史数据，即脸书、亚马逊、苹果、网飞、谷歌和微软。我们用 python 拉数据吧！

**Python 实现:**

首先，我们定义了两个变量来指定数据的开始和结束日期。接下来，使用 pandas DataReader 包，我们提取了这些公司的历史数据。最后，我们只将公司的收盘价数据存储在变量' *stocks_close* '中。现在，我们准备对我们的股票数据进行可视化。

# 对比图

面积图通常被称为“山”图，是标准折线图的一种更简化的解释。他们画出一段时间内的收盘价，线下的区域有阴影。按照代码用 python 中的 Plotly 创建面积图。

**Python 实现:**

输出:

按作者分类的图表

与 Plotly 的高效函数相比，上面生成的面积图是非常基本的。现在，让我们利用 Plotly 强大的功能创建一个定制的面积图。按照代码用 python 中的 Plotly 创建自定义的面积图。

**Python 实现:**

输出:

按作者分类的图表

# 蜡烛图

蜡烛图是技术分析中使用的一种价格图表，显示特定时期内证券的最高价、最低价、开盘价和收盘价。交易者使用蜡烛图来根据过去的模式确定可能的价格变动。烛台在交易时很有用，因为它们在交易者指定的时间内显示四个价格点(开盘价、收盘价、最高价和最低价)。许多算法都基于蜡烛图中显示的相同价格信息。交易通常受情绪支配，这可以从蜡烛图中看出。按照代码用 python 中的 Plotly 生成一个蜡烛图。

**Python 实现:**

输出:

按作者分类的图表

像面积图一样，烛台图表也可以调整和定制，看起来更专业。按照代码用 python 中的 Plotly 生成一个定制的烛台图。

**Python 实现:**

输出:

按作者分类的图表

# OHLC 海图

OHLC 图是一种条形图，显示每个时期的开盘价、最高价、最低价和收盘价。OHLC 图表很有用，因为它们显示了一段时间内的四个主要数据点，收盘价被许多交易者认为是最重要的。图表类型很有用，因为它可以显示增加或减少的势头。当开盘和收盘相距甚远时，它显示出强劲的势头，当开盘和收盘接近时，它显示出犹豫不决或微弱的势头。最高价和最低价显示了该时期的全部价格范围，有助于评估波动性。按照代码用 python 中的 Plotly 制作 OHLC 图。

**Python 实现:**

输出:

按作者分类的图表

现在，让我们使用 Plotly 的函数自定义默认的 OHLC 图表，使绘图更加专业和好看。按照代码使用 python 中的 Plotly 创建自定义的 OHLC 图表。

**Python 实现:**

输出:

按作者分类的图表

# 项目符号图

项目符号图是条形图的变体，旨在将单个主要指标与一个或多个其他指标进行比较，以丰富其含义，并在定性绩效范围的上下文中显示。定性范围显示为一种色调但具有不同强度的块，使色盲的人可以辨别，并将仪表盘上颜色的使用限制在最低限度。我们将使用一个项目符号图来表示一只股票的日波动范围。按照代码用 python 中的 Plotly 创建一个项目符号图。

**Python 实现:**

输出:

按作者分类的图表

# 仪表图

径向仪表图有一个圆弧，它显示单个值来估计实现目标的进度。条形图显示目标值，阴影表示实现目标的进度。仪表图，也称为速度表图。使用量具图，我们将代表一只股票的一天的范围，就像我们使用子弹图一样。按照代码用 python 中的 Plotly 生成仪表图。

**Python 实现:**

输出:

按作者分类的图表

# 最后的想法！

在本文中，我们看到了如何在 python 中使用 Plotly 制作财务图表。从面积图开始，我们学习了用 python 中的 Plotly 创建五种类型的财务可视化。除此之外，还有其他金融可视化工具用于绘制股票以外的数据，但这是值得的。此外，我们还对图表进行了一点点定制。在 Plotly 中有更多的方法和功能可以用来定制和美化图表。您可以在 Plotly 的 python 文档页面上学习和探索更多类型的图表和工具。而且，如果您忘记了遵循任何可视化的编码部分，不要担心，我已经在最后提供了所有可视化的源代码。说完，我们来到了文章的结尾。

**快乐观想！**

完整代码: