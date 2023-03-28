# Python 中实现的前 3 个最佳技术指标

> 原文：<https://medium.com/codex/top-3-best-performing-technical-indicators-implemented-in-python-de7fec949c6b?source=collection_archive---------2----------------------->

## 通过 Python 实现成功交易市场所必需的有效指标

![](img/eaca06c8dd7718822ac7ee776e2cc682.png)

照片由来自[佩克斯](https://www.pexels.com/photo/purple-and-pink-diamond-on-blue-background-5011647/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[罗斯季斯拉夫·乌祖诺夫](https://www.pexels.com/@rostislav-uzunov-3145660?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

市场被成吨成吨的技术指标勒死了，对于新手来说，选择正确的指标将是一场噩梦。因为我花了相当多的时间在这些指标上漫游，并在市场上实现它们，所以我决定创建一个列表，列出 3 个表现最好的指标，以及它们在 Python 中的实现，以帮助初学者进入技术分析领域。事不宜迟，让我们进入文章吧！

**注**:即将到来的指标不是随机选择的，而是根据苹果公司的历史数据进行回测得出的结果挑选出来的。

# 1.差异指数

差异指数是一个动量指标，用于测量股票的当前收盘价与其在指定时间段内的移动平均值之间的距离，并以百分比的形式解释读数。与其他动量振荡器不同，差异指数不限制在某些水平之间，因此是一个无界振荡器。

交易者经常使用差异指数来确定市场的当前势头。如果差异指数的读数高于零，则可以观察到市场的上升势头，类似地，如果该指标的读数低于零，则认为市场处于下降势头。

## 数学

视差指数的计算非常简单。首先，我们必须找出一只股票的收盘价格与指定时间段内的移动平均线之间的差值，并将该差值除以移动平均线，然后乘以 100。以 14 作为回看周期的典型设置的视差指数的计算可以表示如下:

```
**DI 14** = [ **C.PRICE** - **MOVING  AVG 14** ] / [ **MOVING AVG 14** ] * **100**where,
DI 14 = 14-day Disparity Index
MOVING AVG 14 = 14-day Moving Average
C.PRICE = Closing price of the stock
```

## Python 实现

现在，让我们实际使用 Python 来实现上面讨论的数学计算指标。

```
def get_di(data, lookback):
    ma = data.rolling(lookback).mean()
    di = ((data - ma) / ma) * 100
    return di
```

## 代码解释

首先，我们定义一个名为“get_di”的函数，它将股票的收盘价(“数据”)和回望期(“回望”)作为参数。在函数内部，我们首先计算指定数量的回望期的收盘价数据的移动平均值。然后，我们将确定的值代入视差指数公式来计算读数。最后，我们将计算出的读数作为输出返回。

# 2.知道肯定的事

确知事物指标(简称 KST)是一个无界动量振荡器，被交易者广泛用于理解 ROC 指标的读数。KST 指标基于平滑 ROC 的四个不同时间框架，并将收集的数据合并到一个振荡器中。

## 组件和数学

已知事物指示器由两部分组成:

**KST 线:**第一个组成部分是 KST 线本身。为了计算 KST 线的读数，我们必须首先确定四个 roc，分别以 10、15、20、30 作为“n”值。然后分别以 10、10、10、15 作为回顾期，使用简单的移动平均来平滑每个 ROC。这种平滑的 ROC 称为 ROCSMA。在获得四个不同时间段的 ROCSMA 后，我们必须将第一个 ROCSMA 乘以 1，第二个 ROCSMA 乘以 2，第三个 ROCSMA 乘以 3，第四个乘以 4。最后，将这四个乘积相加。KST 线的计算可以用数学方法表示如下:

```
**KL** = (**ROCSMA1** * **1**) + (**ROCSMA2** * **2**) + (**ROCSMA3** * **3**) + (**ROCSMA4** * **4**)where,
KL = KST Line
ROCSMA1 = ROC 10 smoothed with SMA 10
ROCSMA2 = ROC 15 smoothed with SMA 10
ROCSMA3 = ROC 20 smoothed with SMA 10
ROCSMA4 = ROC 30 smoothed with SMA 15
```

**信号线:**现在，确知事物指示器的第二个组件是信号线组件。这个分量只不过是 KST 线的平滑版本。为了平滑 KST 线的值，以 9 作为回望周期的简单移动平均线被广泛使用。信号线的计算如下所示:

```
**SIGNAL LINE** = **SMA9** ( **KST LINE** )
```

## Python 实现

现在是时候用 Python 实现指示器来获取其组件的读数了。

```
def get_roc(close, n):
    difference = close.diff(n)
    nprev_values = close.shift(n)
    roc = (difference / nprev_values) * 100
    return rocdef get_kst(close, sma1, sma2, sma3, sma4, roc1, roc2, roc3, roc4, signal):
    rcma1 = get_roc(close, roc1).rolling(sma1).mean()
    rcma2 = get_roc(close, roc2).rolling(sma2).mean()
    rcma3 = get_roc(close, roc3).rolling(sma3).mean()
    rcma4 = get_roc(close, roc4).rolling(sma4).mean()
    kst = (rcma1 * 1) + (rcma2 * 2) + (rcma3 * 3) + (rcma4 * 4)
    signal = kst.rolling(signal).mean()
    return kst, signal
```

## 代码解释

上述代码可以分为两个不同的部分:ROC 计算和 KST 计算。

**ROC 计算:**我们首先定义一个名为“get_roc”的函数，它将股票的收盘价(“close”)和“n”值(“n”)作为参数。在该函数中，我们首先使用 Pandas 包提供的“diff”函数来获取当前收盘价和指定时间段之前的收盘价之间的差值。在“shift”函数的帮助下，我们考虑了指定时间段之前的收盘价，并将其存储到“nprev_values”变量中。然后，我们将确定的值代入我们之前讨论的 ROC 指标公式，以计算值并最终返回数据。

**KST 计算:**首先，我们定义一个名为“get_kst”的函数，该函数将股票的收盘价(“close”)、平滑 ROC 值的四个回顾期(“sma1”、“sma2”、“sma3”、“sma4”)、ROC 的四个“n”值(“roc1”、“roc2”、“roc3”、“roc4”)以及信号线的回顾期(“signal”)作为参数。在函数内部，我们首先使用 Pandas 包提供的‘rolling’函数和我们之前创建的‘get _ roc’函数来计算四个 ROCSMA 值。然后，我们将计算出的 ROCSMAs 代入我们之前讨论过的公式，以确定 KST 线的读数。然后，我们用“滚动”函数平滑 KST 线的值，以获得信号线的值，并将它们存储到“信号”变量中。最后，我们将返回 KST 指标的两个计算组件。

# 3.真实强度指数

真实力量指数(TSI)是一个动量振荡器，主要由交易者用来确定市场是向上还是向下的动量，并随之交易。它还用于识别市场的当前状态，超买或超卖，但这不是该指标的主要优势。

## 组件和数学

与“确信无疑”指标一样，真实实力指数也由两部分组成:

**TSI 线:**第一部分是 TSI 线本身，它是通过首先确定实际价格变化(当前收盘价减去前一收盘价)和绝对价格变化(实际价格变化的绝对值)来计算的。然后，实际价格变化和绝对价格变化都采用周期数为 25 的均线(多头)。然后这两个 EMA 被 13 天的指数移动平均线平滑。用两个 EMA 平滑数据序列的过程称为双重平滑，这样做的目的是消除数据中的噪声。现在，双重平滑的实际价格变化除以双重平滑的绝对价格变化，然后乘以 100，以获得 TSI 线的读数。请注意，我们考虑的参数(25，13)是典型设置，但可以相应调整。计算可能很模糊，但如果我们以公式或图示的形式来解释，就很容易理解:

```
**TSI LINE** = [ **DS. ACTUAL PC** / **DS. ABSOLUTE PC** ] * **100**where,**DS. ACTUAL PC** = Double smoothed actual price change with the length of 25 and 13**DS. ABSOLUTE PC** = Double smoothed absolute price change with the length of 25 and 13
```

**信号线:**下一个组件是信号线组件，它只是 TSI 在指定周期数(在 7 到 12 个周期内)的指数移动平均值。大多数交易者喜欢日交易周期接近 7，长期投资周期接近 12。在本文中，我们将周期数定为 12，因为我们将处理每日时间段的股票数据，而不是分钟时间段的数据。该计算可以表示如下:

```
**SIGNAL LINE** = **EXP.MA 13** [ **TSI LINE** ]
```

## Python 实现

与其他两个指标不同，真正的实力指数有一个相当长的代码，因为它有复杂的数学公式来计算其组成部分。现在，让我们用 Python 编写指示器的代码。

```
def get_tsi(close, long, short, signal):
    diff = close - close.shift(1)
    abs_diff = abs(diff)

    diff_smoothed = diff.ewm(span = long, adjust = False).mean()
    diff_double_smoothed = diff_smoothed.ewm(span = short, adjust = False).mean()
    abs_diff_smoothed = abs_diff.ewm(span = long, adjust = False).mean()
    abs_diff_double_smoothed = abs_diff_smoothed.ewm(span = short, adjust = False).mean()

    tsi = (diff_double_smoothed / abs_diff_double_smoothed) * 100
    signal = tsi.ewm(span = signal, adjust = False).mean()
    tsi = tsi[tsi.index >= '2020-01-01'].dropna()
    signal = signal[signal.index >= '2020-01-01'].dropna()

    return tsi, signal
```

## 代码解释

首先，我们定义一个名为“get_tsi”的函数，它将股票的收盘价数据(“收盘”)、长均线的回望期(“长”)和短均线的回望期(“短”)以及信号线的回望期(“信号”)作为参数。在函数内部，我们首先计算实际价格变化(' diff ')和绝对价格变化(' abs_diff ')并将其存储到各自的变量中。

然后，使用 Pandas 软件包提供的“ewm”函数来确定指数移动平均值，我们对之前计算的价格变化进行双重平滑，以获得双重平滑的实际价格变化(“diff_double_smoothed”)和双重平滑的绝对价格变化(“abs_diff_double_smoothed”)。

然后，我们将双平滑值代入 TSI 线的公式，以确定其读数。为了计算信号线的值，我们采用特定周期数的已确定 TSI 线读数的 EMA。最后，我们返回计算的组件。

> **荣誉奖:科博克曲线，MACD，阿隆，和令人敬畏的振荡器**

# 最后的想法！

要记住的一个重要方面是，如果构建的策略没有效率，伟大的指标就会过时。所以要确保你的武器库中不仅有好的指标，还有适合市场的交易策略。此外，不要忘记回测和评估策略，以得出实际的见解。如果没有这两个过程，得出结论并将其部署到现实世界市场是致命的。

话虽如此，你已经到了文章的结尾。如果您忘记了遵循任何编码部分，不要担心。我在文章底部提供了完整的源代码。希望你能从这篇文章中学到一些新的有用的东西。

**主办:** [EOD 历史数据](https://eodhistoricaldata.com/r/?ref=DHY3H8NT)是金融应用编程接口市场的领导者之一，提供各种各样的应用编程接口，从基本的每日市场数据到高度可定制的应用编程接口，如金融新闻应用编程接口和股票筛选应用编程接口。他们所有的 API 都是以一种本质上易于使用的方式设计的，因此初学者可以毫无障碍地使用它们。我个人使用过 [EOD 历史数据公司的](https://eodhistoricaldata.com/r/?ref=DHY3H8NT)API，从我的经验来看，他们的 API 既适合专业人士也适合业余爱好者，用于辅助项目和构建企业级应用程序。

## 完整代码:

```
**# DISPARITY INDEX**def get_di(data, lookback):
    ma = data.rolling(lookback).mean()
    di = ((data - ma) / ma) * 100
    return di**# KNOW SURE THING**def get_roc(close, n):
    difference = close.diff(n)
    nprev_values = close.shift(n)
    roc = (difference / nprev_values) * 100
    return rocdef get_kst(close, sma1, sma2, sma3, sma4, roc1, roc2, roc3, roc4, signal):
    rcma1 = get_roc(close, roc1).rolling(sma1).mean()
    rcma2 = get_roc(close, roc2).rolling(sma2).mean()
    rcma3 = get_roc(close, roc3).rolling(sma3).mean()
    rcma4 = get_roc(close, roc4).rolling(sma4).mean()
    kst = (rcma1 * 1) + (rcma2 * 2) + (rcma3 * 3) + (rcma4 * 4)
    signal = kst.rolling(signal).mean()
    return kst, signal**# TRUE STRENGTH INDEX**def get_tsi(close, long, short, signal):
    diff = close - close.shift(1)
    abs_diff = abs(diff)

    diff_smoothed = diff.ewm(span = long, adjust = False).mean()
    diff_double_smoothed = diff_smoothed.ewm(span = short, adjust = False).mean()
    abs_diff_smoothed = abs_diff.ewm(span = long, adjust = False).mean()
    abs_diff_double_smoothed = abs_diff_smoothed.ewm(span = short, adjust = False).mean()

    tsi = (diff_double_smoothed / abs_diff_double_smoothed) * 100
    signal = tsi.ewm(span = signal, adjust = False).mean()
    tsi = tsi[tsi.index >= '2020-01-01'].dropna()
    signal = signal[signal.index >= '2020-01-01'].dropna()

    return tsi, signal
```