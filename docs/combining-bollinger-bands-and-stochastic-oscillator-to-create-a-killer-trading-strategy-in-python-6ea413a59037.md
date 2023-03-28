# 用 Python 结合布林线和随机振荡指标创建黑仔交易策略

> 原文：<https://medium.com/codex/combining-bollinger-bands-and-stochastic-oscillator-to-create-a-killer-trading-strategy-in-python-6ea413a59037?source=collection_archive---------0----------------------->

## 通过使用两个强大的技术指标来提高策略性能的详细指南

![](img/8798bac650e963572f76edb9f5952f4e.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

技术指标是伟大和有效的，但它们都有一个特定的缺点，那就是揭示错误的交易信号，因此交易股票可能会导致灾难性的结果。为了解决这个问题，最好是引入两个不同的技术指标，它们的特征因交易策略而异，这样我们就可以从真实的交易信号中过滤出虚假的交易信号。

在本文中，我们将尝试用两个技术指标来构建交易策略，它们是布林线和 Python 中的随机振荡指标。我们还将对该策略进行回溯测试，并将回报率与 SPY ETF(一种专门用于跟踪标准普尔 500 市场指数走势的 ETF)的回报率进行比较。事不宜迟，让我们进入文章吧！

在继续之前，如果你想在没有任何代码的情况下回溯测试你的交易策略，有一个解决方案。这是[的后验区](https://www.backtestzone.com/)。这是一个平台，可以免费对不同类型的可交易资产的任意数量的交易策略进行回溯测试，无需编码。点击这里的链接，你可以马上使用这个工具:[https://www.backtestzone.com/](https://www.backtestzone.com/)

# 布林线

在开始探索布林线之前，有必要知道什么是简单移动平均线。简单移动平均线只不过是给定一段时间内股票的平均价格。现在，布林线是在特定的标准差水平上，在给定股票的 SMA 上下绘制的趋势线。为了更好地理解布林线，看看下面的图表，它代表了用 SMA 20 计算的苹果股票的布林线。

![](img/c1099666e8f2b825052ece5a86e475e7.png)

作者图片

布林线是观察一段时间内给定股票波动的好方法。当上下波段之间的空间或距离越小时，股票的波动性越低。同样，当上下波段之间的空间或距离越大，股票的波动水平越高。在观察图表时，你可以观察到一条名为“中间 BB 20”的趋势线，它就是苹果股票的 SMA 20。计算股票上限和下限的公式如下:

```
**UPPER_BB = STOCK SMA + SMA STANDARD DEVIATION * 2
LOWER_BB = STOCK SMA - SMA STANDARD DEVIATION * 2**
```

# 随机振荡器

随机振荡器是一种基于动量的领先指标，广泛用于识别市场是处于超买还是超卖的状态。这就引出了我们的下一个问题。在令人担忧的市场中，什么是超买和超卖？当市场的趋势看起来非常看涨并且一定会盘整时，股票被认为是超买。类似地，当市场趋势似乎极度看跌并有反弹趋势时，股票到达超卖区域。

由于其归一化功能，随机振荡器的值总是在 0 到 100 之间。一般超买和超卖水平分别被认为是 70 和 30，但它可能因人而异。随机振荡器包括两个主要组件:

*   **%K 线:**这条线是随机振荡器指标中最重要、最核心的组成部分。它也被称为快速随机指标。这条线的唯一目的是表达市场的当前状态(超买或超卖)。这条线的计算方法是从股票的收盘价中减去股票在指定时间段内达到的最低价格，然后将该差值除以从最高股票价格中减去股票在指定时间段内达到的最低价格所得的值。通过将上述步骤计算出的值乘以 100，得到最终值。用最流行的设置 14 作为周期数来计算%K 线的方法可以表示如下:

```
**%K = 100 * ((14 DAY CLOSING PRICE - 14 DAY LOWEST PRICE) - (14 DAY HIGHEST PRICE - 14 DAY LOWEST PRICE))**
```

*   **%D 线:**又称慢随机指标，无非是%K 线在特定时期的移动平均线。它也被称为%K 线的平滑版本，因为%D 线的线图看起来比%K 线更平滑。%D 行的标准设置是周期数为 3。

这就是计算随机振子分量的整个过程。现在，让我们分析一个图表，其中苹果的收盘价数据与随机振荡器一起绘制，随机振荡器分别以 14 和 3 作为%K 线和% D 线的回望期，以建立对该指标及其使用方式的牢固理解。

![](img/5ad64997f9a7e37d5499947706bad461.png)

作者图片

该图细分为两个面板:上面板和下面板。上面的面板代表苹果收盘价的线图。下面的面板包括随机振荡器的组件。作为领先指标，随机振荡指标不能和收盘价一起绘制，因为指标值和收盘价变化很大。因此，它与收盘价(在我们的例子中低于收盘价)分开绘制。

我们之前讨论过的成分%K 线和%D 线分别用蓝色和橙色绘制。您还可以注意到%K 和%D 线上方和下方的两条额外的黑色虚线。它是被称为波段的随机振荡器的附加组件。这些波段用来突出超买和超卖的区域。如果%K 线和%D 线都在高波段上方交叉，那么股票被认为是超买。同样，当%K 和%D 线都穿过低波段时，股票被认为超卖。

# 交易策略

现在我们已经对这两个技术指标有了一些基本的了解。让我们继续使用布林线和随机振荡器来执行本文中的交易策略。

如果随机振荡指标的前一天读数在 30 以上，当天读数在 30 以下，最后收盘价在较低区间以下，我们的交易策略将显示买入信号。类似地，当随机振荡器的组件读数低于 70，当前组件读数高于 70，最后收盘价高于上限时，就会显示卖出信号。该策略可以表示如下:

```
IF **PREV_ST_COM > 30** AND **CUR_ST_COMP < 30** AND **CL < LOWER_BB** ==> **BUY**
IF **PREV_ST_COM > 70** AND **CUR_ST_COMP < 70** AND **CL < UPPER_BB** ==> **SELL**where,
PRE_ST_COM = Previous Day Stochastic Oscillator components' readings
CUR_ST_COM = Current Day Stochastic Oscillator components' readings
CL = Last Closing Price
LOWER_BB = Current Day Lower Band reading
UPPER_BB = Current Day Upper Band reading
```

就是这样！我们的理论部分到此结束，让我们进入编程部分，我们将首先使用 Python 从头构建指标，构建讨论的交易策略，对苹果股票数据进行回溯测试，最后将结果与 SPY ETF 的结果进行比较。来做点编码吧！在继续之前，关于免责声明的一个注意事项:本文的唯一目的是教育人们，必须被视为一个信息，而不是投资建议等。

# 用 Python 实现

编码部分分为以下几个步骤:

```
**1\. Importing Packages
2\. Extracting Stock Data from Twelve Data
3\. Bollinger Bands Calculation
4\. Stochastic Oscillator Calculation
5\. Creating the Trading Strategy
6\. Creating our Position
7\. Backtesting
8\. SPY ETF Comparison**
```

我们将按照上面列表中提到的顺序，系好安全带，跟随每一个即将到来的编码部分。

## 步骤 1:导入包

将所需的包导入 python 环境是一个不可跳过的步骤。主要的包是处理数据的 Pandas，处理数组和复杂函数的 NumPy，用于绘图的 Matplotlib，以及进行 API 调用的请求。二级包是数学函数的 Math 和字体定制的 Termcolor(可选)。

**Python 实现:**

```
**# IMPORTING PACKAGES**import pandas as pd
import requests
import numpy as np
import matplotlib.pyplot as plt
from math import floor
from termcolor import colored as clplt.rcParams[‘figure.figsize’] = (20, 10)
plt.style.use(‘fivethirtyeight’)
```

现在我们已经将所有需要的包导入到 python 中。我们用十二数据的 API 端点来拉一下苹果的历史数据。

## 步骤 2:从 12 个数据中提取股票数据

在这一步，我们将使用由[twelvedata.com](https://twelvedata.com/)提供的 API 端点提取苹果的历史股票数据。在此之前，请注意 twelvedata.com:十二数据公司是领先的市场数据提供商之一，拥有大量适用于所有类型市场数据的 API 端点。它非常容易与十二数据提供的 API 进行交互，并且拥有有史以来最好的文档。此外，确保你有一个 twelvedata.com[的账户，只有这样，你才能访问你的 API 密匙(用 API 提取数据的重要元素)。](https://twelvedata.com/)

**Python 实现:**

```
**# EXTRACTING STOCK DATA** 
def get_historical_data(symbol, start_date):
    api_key = 'YOUR API KEY'
    api_url = f'https://api.twelvedata.com/time_series?symbol={symbol}&interval=1day&outputsize=5000&apikey={api_key}'
    raw_df = requests.get(api_url).json()
    df = pd.DataFrame(raw_df['values']).iloc[::-1].set_index('datetime').astype(float)
    df = df[df.index >= start_date]
    df.index = pd.to_datetime(df.index)
    return df

aapl = get_historical_data('AAPL', '2010-01-01')
aapl.tail()
```

**输出:**

![](img/2babe68c9afedb2af51f6d5779142fae.png)

作者图片

**代码解释:**我们做的第一件事是定义一个名为‘get _ historical _ data’的函数，该函数将股票的符号(‘symbol’)和历史数据的起始日期(‘start _ date’)作为参数。在函数内部，我们定义了 API 键和 URL，并将它们存储到各自的变量中。接下来，我们使用“get”函数提取 JSON 格式的历史数据，并将其存储到“raw_df”变量中。在对原始 JSON 数据进行清理和格式化之后，我们将以干净的 Pandas 数据帧的形式返回它。最后，我们调用创建的函数从 2010 年开始提取苹果的历史数据，并将其存储到‘AAPL’变量中。

## 步骤 3:布林线计算

在这一步，我们将按照之前讨论的方法和公式计算布林线的组成部分。

**Python 实现:**

```
**# BOLLINGER BANDS CALCULATION** 
def sma(data, lookback):
    sma = data.rolling(lookback).mean()
    return sma

def get_bb(data, lookback):
    std = data.rolling(lookback).std()
    upper_bb = sma(data, lookback) + std * 2
    lower_bb = sma(data, lookback) - std * 2
    middle_bb = sma(data, lookback)
    return upper_bb, lower_bb, middle_bb

aapl['upper_bb'], aapl['middle_bb'], aapl['lower_bb'] = get_bb(aapl['close'], 20)
aapl = aapl.dropna()
aapl.tail()
```

**输出:**

![](img/a09d7aa26256964865653498fa6dc482.png)

作者图片

**代码解释:**以上可以分为两部分:SMA 计算，和布林线计算。

sma 计算:首先，我们定义一个名为“SMA”的函数，它以股票价格(“数据”)和周期数(“回看”)作为参数。在函数内部，我们使用 Pandas 包提供的“滚动”函数来计算给定周期数的 SMA。最后，我们将计算出的值存储到“sma”变量中并返回它们。

布林线计算:我们首先定义一个名为“get_bb”的函数，它将股票价格(“数据”)和周期数作为参数(“回看”)。在函数内部，我们使用“滚动”和“标准”函数来计算给定股票数据的标准偏差，并将计算出的标准偏差值存储到“标准”变量中。接下来，我们使用各自的公式计算布林线值，最后，我们返回计算值。我们使用创建的“bb”函数将布林线值存储到我们的“aapl”数据框架中。

## 步骤 4:随机振荡器计算

在这一步，我们将按照之前讨论过的方法和公式来计算随机振荡器的分量。

**Python 实现:**

```
**# STOCHASTIC OSCILLATOR CALCULATION** 
def get_stoch_osc(high, low, close, k_lookback, d_lookback):
    lowest_low = low.rolling(k_lookback).min()
    highest_high = high.rolling(k_lookback).max()
    k_line = ((close - lowest_low) / (highest_high - lowest_low)) * 100
    d_line = k_line.rolling(d_lookback).mean()
    return k_line, d_line

aapl['%k'], aapl['%d'] = get_stoch_osc(aapl['high'], aapl['low'], aapl['close'], 14, 3)
aapl.tail()
```

**输出:**

![](img/a5760d13e6fb2d66246520e5e12b65cd.png)

作者图片

**代码解释:**我们首先定义一个名为“get_stoch_osc”的函数，它分别将股票的高点(“高点”)、低点(“低点”)、收盘价数据(“收盘”)以及%K 线(“K _ 回看”)和%D 线(“D _ 回看”)的回看周期作为参数。在函数内部，我们首先使用 Pandas 软件包提供的“滚动”、“最小”和“最大”函数计算指定周期数的最低下限和最高上限数据点，并将这些值存储到“最低下限”和“最高上限”变量中。

接下来是%K 线计算，我们将公式代入代码，并将读数存储到“k 线”变量中，然后，我们计算%D 线，这不过是获取指定周期数的%K 线读数的 SMA。最后，我们将返回值并调用函数来存储 Apple 的随机振荡器读数，分别以 14 和 3 作为%K 和%D 线的回望周期。

## 步骤 5:创建交易策略

在这一步，我们将在 python 中实现讨论过的布林线和随机振荡器交易策略。

**Python 实现:**

```
**# TRADING STRATEGY** 
def bb_stoch_strategy(prices, k, d, upper_bb, lower_bb):
    buy_price = []
    sell_price = []
    bb_stoch_signal = []
    signal = 0

    for i in range(len(prices)):
        if k[i-1] > 30 and d[i-1] > 30 and k[i] < 30 and d[i] < 30 and prices[i] < lower_bb[i]:
            if signal != 1:
                buy_price.append(prices[i])
                sell_price.append(np.nan)
                signal = 1
                bb_stoch_signal.append(signal)
            else:
                buy_price.append(np.nan)
                sell_price.append(np.nan)
                bb_stoch_signal.append(0)
        elif k[i-1] < 70 and d[i-1] < 70 and k[i] > 70 and d[i] > 70 and prices[i] > upper_bb[i]:
            if signal != -1:
                buy_price.append(np.nan)
                sell_price.append(prices[i])
                signal = -1
                bb_stoch_signal.append(signal)
            else:
                buy_price.append(np.nan)
                sell_price.append(np.nan)
                bb_stoch_signal.append(0)
        else:
            buy_price.append(np.nan)
            sell_price.append(np.nan)
            bb_stoch_signal.append(0)

    sell_price[-1] = prices[-1]
    bb_stoch_signal[-1] = -1
    return buy_price, sell_price, bb_stoch_signal

buy_price, sell_price, bb_stoch_signal = bb_stoch_strategy(aapl['close'], aapl['%k'], aapl['%d'], aapl['upper_bb'], aapl['lower_bb'])
```

**代码解释:**首先，我们定义一个名为‘bb _ stoch _ strategy’的函数，它将股票价格(‘prices’)、k 线读数(‘K’)、D 线读数(‘D’)、布林带上轨(‘Upper _ bb’)和布林下轨(‘Lower _ bb’)作为参数。

在该函数中，我们创建了三个空列表(buy_price、sell_price 和 bb_stoch_signal ),在创建交易策略时，这些值将被追加到这些列表中。

之后，我们通过 for 循环实施交易策略。在 for 循环内部，我们传递某些条件，如果条件得到满足，相应的值将被追加到空列表中。如果购买股票的条件得到满足，买入价将被追加到“buy_price”列表中，信号值将被追加为 1，表示购买股票。类似地，如果卖出股票的条件得到满足，卖价将被追加到“sell_price”列表中，信号值将被追加为-1，表示卖出股票。最后，我们返回附加了值的列表。然后，我们调用创建的函数并将值存储到各自的变量中。

## 步骤 6:创建我们的职位

在这一步中，我们将创建一个列表，如果我们持有股票，该列表将指示 1；如果我们不拥有或持有股票，该列表将指示 0。

**Python 实现:**

```
**# POSITION**position = []
for i in range(len(bb_stoch_signal)):
    if bb_stoch_signal[i] > 1:
        position.append(0)
    else:
        position.append(1)

for i in range(len(aapl['close'])):
    if bb_stoch_signal[i] == 1:
        position[i] = 1
    elif bb_stoch_signal[i] == -1:
        position[i] = 0
    else:
        position[i] = position[i-1]

k = aapl['%k']
d = aapl['%d']
upper_bb = aapl['upper_bb'] 
lower_bb = aapl['lower_bb']
close_price = aapl['close']
bb_stoch_signal = pd.DataFrame(bb_stoch_signal).rename(columns = {0:'bb_stoch_signal'}).set_index(aapl.index)
position = pd.DataFrame(position).rename(columns = {0:'bb_stoch_position'}).set_index(aapl.index)

frames = [close_price, k, d, upper_bb, lower_bb, bb_stoch_signal, position]
strategy = pd.concat(frames, join = 'inner', axis = 1)

strategy.tail()
```

**输出:**

![](img/23430193ebcc99c297fc891193953294.png)

作者图片

**代码解释:**首先，我们创建一个名为‘position’的空列表。我们传递两个 for 循环，一个是为“位置”列表生成值，以匹配“信号”列表的长度。另一个 for 循环是我们用来生成实际位置值的循环。

在第二个 for 循环中，我们对“signal”列表的值进行迭代，而“position”列表的值被附加到满足哪个条件上。如果我们持有股票，头寸的价值仍为 1；如果我们卖出或不持有股票，头寸的价值仍为 0。最后，我们正在进行一些数据操作，将所有创建的列表合并到一个数据帧中。

从显示的输出中，我们可以看到，在前四行中，我们在股票中的位置保持为 1(因为交易信号没有任何变化)，但是当交易信号代表买入信号(-1)时，我们的位置突然变为 0。我们的头寸将保持-1，直到交易信号发生一些变化。现在是时候实现一些回溯测试过程了！

## 步骤 7:回溯测试

在继续之前，有必要知道什么是回溯测试。回溯测试是查看我们的交易策略在给定股票数据上表现如何的过程。在我们的例子中，我们将对我们的布林线随机振荡器交易策略在苹果股票数据上实施一个回溯测试过程。

**Python 实现:**

```
**# BACKTESTING** 
aapl_ret = pd.DataFrame(np.diff(aapl['close'])).rename(columns = {0:'returns'})
bb_stoch_strategy_ret = []

for i in range(len(aapl_ret)):
    returns = aapl_ret['returns'][i]*strategy['bb_stoch_position'][i]
    bb_stoch_strategy_ret.append(returns)

bb_stoch_strategy_ret_df = pd.DataFrame(bb_stoch_strategy_ret).rename(columns = {0:'bb_stoch_returns'})
investment_value = 100000
number_of_stocks = floor(investment_value/aapl['close'][0])
bb_stoch_investment_ret = []

for i in range(len(bb_stoch_strategy_ret_df['bb_stoch_returns'])):
    returns = number_of_stocks*bb_stoch_strategy_ret_df['bb_stoch_returns'][i]
    bb_stoch_investment_ret.append(returns)

bb_stoch_investment_ret_df = pd.DataFrame(bb_stoch_investment_ret).rename(columns = {0:'investment_returns'})
total_investment_ret = round(sum(bb_stoch_investment_ret_df['investment_returns']), 2)
profit_percentage = floor((total_investment_ret/investment_value)*100)
print(cl('Profit gained from the BB STOCH strategy by investing $100k in AAPL : {}'.format(total_investment_ret), attrs = ['bold']))
print(cl('Profit percentage of the BB STOCH strategy : {}%'.format(profit_percentage), attrs = ['bold']))
```

**输出:**

```
**Profit gained from the BB STOCH strategy by investing $100k in AAPL : 1713231.15**
**Profit percentage of the BB STOCH strategy : 1713%**
```

**代码解释:**首先，我们使用 NumPy 包提供的“diff”函数计算苹果股票的回报，并将其作为数据帧存储到“aapl_ret”变量中。接下来，我们传递一个 for 循环来迭代' aapl_ret '变量的值，以计算我们从交易策略中获得的回报，这些回报值被追加到' bb_stoch_strategy_ret '列表中。接下来，我们将“bb_stoch_strategy_ret”列表转换为数据帧，并将其存储到“bb_stoch_strategy_ret_df”变量中。

接下来是回溯测试过程。我们将通过投资 10 万美元到我们的交易策略中来回测我们的策略。首先，我们将投资金额存储到“投资值”变量中。之后，我们正在计算使用投资金额可以购买的苹果股票数量。你可以注意到，我使用了 Math 软件包提供的“floor”函数，因为当投资金额除以苹果股票的收盘价时，它会输出一个十进制数。股票数量应该是整数，而不是小数。使用“底数”函数，我们可以去掉小数。请记住,“floor”函数比“round”函数要复杂得多。然后，我们传递一个 for 循环来查找投资回报，后面是一些数据操作任务。

最后，我们打印了我们在交易策略中投资 10 万美元的总回报，显示我们在大约 10 年半的时间里获得了大约 170 万美元的利润，利润率为 1713%。太好了！现在，让我们将我们的回报与 SPY ETF(一种旨在跟踪标准普尔 500 股票市场指数的 ETF)的回报进行比较。

## 第八步:间谍 ETF 对比

这一步是可选的，但强烈推荐，因为我们可以了解我们的交易策略相对于基准(间谍 ETF)的表现如何。在这一步中，我们将使用我们创建的“get_historical_data”函数提取 SPY ETF 数据，并将我们从 SPY ETF 获得的回报与我们在 Apple 上的布林带随机振荡器交易策略回报进行比较。

你可能已经注意到，在我所有的算法交易文章中，我没有将策略结果与标准普尔 500 市场指数本身进行比较，而是与 SPY ETF 进行比较，这是因为大多数股票数据提供商(如 12 Data)不提供标准普尔 500 指数数据。所以，我别无选择，只能选择间谍 ETF。如果你有幸得到标准普尔 500 市场指数数据，建议用它来做比较，而不是任何 ETF。

**Python 实现:**

```
**# SPY ETF COMPARISON**

def get_benchmark(start_date, investment_value):
    spy = get_historical_data('SPY', start_date)['close']
    benchmark = pd.DataFrame(np.diff(spy)).rename(columns = {0:'benchmark_returns'})

    investment_value = investment_value
    number_of_stocks = floor(investment_value/spy[0])
    benchmark_investment_ret = []

    for i in range(len(benchmark['benchmark_returns'])):
        returns = number_of_stocks*benchmark['benchmark_returns'][i]
        benchmark_investment_ret.append(returns)

    benchmark_investment_ret_df = pd.DataFrame(benchmark_investment_ret).rename(columns = {0:'investment_returns'})
    return benchmark_investment_ret_df

benchmark = get_benchmark('2010-01-01', 100000)

investment_value = 100000
total_benchmark_investment_ret = round(sum(benchmark['investment_returns']), 2)
benchmark_profit_percentage = floor((total_benchmark_investment_ret/investment_value)*100)
print(cl('Benchmark profit by investing $100k : {}'.format(total_benchmark_investment_ret), attrs = ['bold']))
print(cl('Benchmark Profit percentage : {}%'.format(benchmark_profit_percentage), attrs = ['bold']))
print(cl('BB STOCH Strategy profit is {}% higher than the Benchmark Profit'.format(profit_percentage - benchmark_profit_percentage), attrs = ['bold']))
```

**输出:**

```
**Benchmark profit by investing $100k : 284127.48**
**Benchmark Profit percentage : 284%**
**BB STOCH Strategy profit is 1429% higher than the Benchmark Profit**
```

**代码解释:**此步骤中使用的代码与前一个回溯测试步骤中使用的代码几乎相似，但我们不是投资苹果，而是通过不实施任何交易策略来投资 SPY ETF。从输出可以看出，我们的布林带随机振荡器交易策略已经跑赢了 SPY ETF 1429%。太棒了。

# 最后的想法！

在彻底粉碎了理论和程序部分之后，我们成功地建立了一个有两个技术指标的交易策略，实际上取得了不错的回报。

在我看来，考虑两个技术指标来建立交易策略是技术分析中最基本和最重要的一步，我们必须确保我们以正确的方式来做。这将有助于我们在使用错误信号的技术指标时克服一个巨大的障碍，这很好，因为让我们的交易与非真实信号保持一致将导致奇怪的结果，并最终导致资本整体枯竭。

谈到改进，改进这篇文章并使其更上一层楼的一个方法是创建一个实际的交易策略。在本文中，如果相应的条件得到满足，我们的程序将买入股票，卖出股票也是如此，但在现实世界的市场中实现时，情况会有所不同。

例如，对于现实市场中的每笔交易，都会有一部分资金作为佣金转移给经纪人，但在我们的策略中，我们没有考虑这样的因素，甚至对我们的回报也有一些影响。因此，用现实的方法构建我们的交易策略可能会帮助我们获得更多实际的见解。

话虽如此，你已经到了文章的结尾。如果您忘记了遵循任何编码部分，不要担心。我在最后提供了完整的源代码。希望你能从这篇文章中学到一些新的有用的东西。

**主办:** [EOD 历史数据](https://eodhistoricaldata.com/r/?ref=DHY3H8NT)是金融应用编程接口市场的领导者之一，提供各种各样的应用编程接口，从基本的每日市场数据到高度可定制的应用编程接口，如金融新闻应用编程接口和股票筛选应用编程接口。他们所有的 API 都是以一种本质上易于使用的方式设计的，因此初学者可以毫无障碍地使用它们。我个人使用过 [EOD 历史数据公司的](https://eodhistoricaldata.com/r/?ref=DHY3H8NT)API，从我的经验来看，他们的 API 既适合专业人士也适合业余爱好者，用于辅助项目和构建企业级应用程序。

## 完整代码:

```
# IMPORTING PACKAGES

import pandas as pd
import requests
import numpy as np
import matplotlib.pyplot as plt
from math import floor
from termcolor import colored as cl

plt.rcParams['figure.figsize'] = (20, 10)
plt.style.use('fivethirtyeight')

# EXTRACTING STOCK DATA

def get_historical_data(symbol, start_date):
    api_key = 'YOUR API KEY'
    api_url = f'https://api.twelvedata.com/time_series?symbol={symbol}&interval=1day&outputsize=5000&apikey={api_key}'
    raw_df = requests.get(api_url).json()
    df = pd.DataFrame(raw_df['values']).iloc[::-1].set_index('datetime').astype(float)
    df = df[df.index >= start_date]
    df.index = pd.to_datetime(df.index)
    return df

aapl = get_historical_data('AAPL', '2010-01-01')
aapl.tail()

# BOLLINGER BANDS CALCULATION

def sma(data, lookback):
    sma = data.rolling(lookback).mean()
    return sma

def get_bb(data, lookback):
    std = data.rolling(lookback).std()
    upper_bb = sma(data, lookback) + std * 2
    lower_bb = sma(data, lookback) - std * 2
    middle_bb = sma(data, lookback)
    return upper_bb, lower_bb, middle_bb

aapl['upper_bb'], aapl['middle_bb'], aapl['lower_bb'] = get_bb(aapl['close'], 20)
aapl = aapl.dropna()
aapl.tail()

# STOCHASTIC OSCILLATOR CALCULATION

def get_stoch_osc(high, low, close, k_lookback, d_lookback):
    lowest_low = low.rolling(k_lookback).min()
    highest_high = high.rolling(k_lookback).max()
    k_line = ((close - lowest_low) / (highest_high - lowest_low)) * 100
    d_line = k_line.rolling(d_lookback).mean()
    return k_line, d_line

aapl['%k'], aapl['%d'] = get_stoch_osc(aapl['high'], aapl['low'], aapl['close'], 14, 3)
aapl.tail()

# PLOTTING THE DATA

plot_data = aapl[aapl.index >= '2020-01-01']

plt.plot(plot_data['close'], linewidth = 2.5)
plt.plot(plot_data['upper_bb'], label = 'UPPER BB 20', linestyle = '--', linewidth = 1, color = 'black')
plt.plot(plot_data['middle_bb'], label = 'MIDDLE BB 20', linestyle = '--', linewidth = 1.2, color = 'grey')
plt.plot(plot_data['lower_bb'], label = 'LOWER BB 20', linestyle = '--', linewidth = 1, color = 'black')
plt.title('AAPL BB 20')
plt.legend(loc = 'upper left')
plt.show()

ax1 = plt.subplot2grid((14,1), (0,0), rowspan = 7, colspan = 1)
ax2 = plt.subplot2grid((15,1), (9,0), rowspan = 6, colspan = 1)
ax1.plot(plot_data['close'], linewidth = 2.5)
ax1.set_title('AAPL STOCK PRICES')
ax2.plot(plot_data['%k'], color = 'deepskyblue', linewidth = 1.5, label = '%K')
ax2.plot(plot_data['%d'], color = 'orange', linewidth = 1.5, label = '%D')
ax2.axhline(70, color = 'black', linewidth = 1, linestyle = '--')
ax2.axhline(30, color = 'black', linewidth = 1, linestyle = '--')
ax2.set_title(f'AAPL STOCH 14,3')
ax2.legend(loc = 'right')
plt.show()

# TRADING STRATEGY

def bb_stoch_strategy(prices, k, d, upper_bb, lower_bb):
    buy_price = []
    sell_price = []
    bb_stoch_signal = []
    signal = 0

    for i in range(len(prices)):
        if k[i-1] > 30 and d[i-1] > 30 and k[i] < 30 and d[i] < 30 and prices[i] < lower_bb[i]:
            if signal != 1:
                buy_price.append(prices[i])
                sell_price.append(np.nan)
                signal = 1
                bb_stoch_signal.append(signal)
            else:
                buy_price.append(np.nan)
                sell_price.append(np.nan)
                bb_stoch_signal.append(0)
        elif k[i-1] < 70 and d[i-1] < 70 and k[i] > 70 and d[i] > 70 and prices[i] > upper_bb[i]:
            if signal != -1:
                buy_price.append(np.nan)
                sell_price.append(prices[i])
                signal = -1
                bb_stoch_signal.append(signal)
            else:
                buy_price.append(np.nan)
                sell_price.append(np.nan)
                bb_stoch_signal.append(0)
        else:
            buy_price.append(np.nan)
            sell_price.append(np.nan)
            bb_stoch_signal.append(0)

    sell_price[-1] = prices[-1]
    bb_stoch_signal[-1] = -1
    return buy_price, sell_price, bb_stoch_signal

buy_price, sell_price, bb_stoch_signal = bb_stoch_strategy(aapl['close'], aapl['%k'], aapl['%d'], aapl['upper_bb'], aapl['lower_bb'])

# PLOTTING TRADING SIGNALS

ax1 = plt.subplot2grid((14,1), (0,0), rowspan = 7, colspan = 1)
ax2 = plt.subplot2grid((15,1), (9,0), rowspan = 6, colspan = 1)
ax1.plot(aapl['close'], linewidth = 2.5)
ax1.plot(aapl['upper_bb'], label = 'UPPER BB 20', linestyle = '--', linewidth = 1, color = 'black')
ax1.plot(aapl['middle_bb'], label = 'MIDDLE BB 20', linestyle = '--', linewidth = 1.2, color = 'grey')
ax1.plot(aapl['lower_bb'], label = 'LOWER BB 20', linestyle = '--', linewidth = 1, color = 'black')
ax1.plot(aapl.index, buy_price, marker = '^', markersize = 10, color = 'green', label = 'BUY SIGNAL')
ax1.plot(aapl.index, sell_price, marker = 'v', markersize = 10, color = 'r', label = 'SELL SIGNAL')
ax1.set_title('AAPL BB 20')
ax1.legend(loc = 'right')
ax2.plot(aapl['%k'], color = 'deepskyblue', linewidth = 1.5, label = '%K')
ax2.plot(aapl['%d'], color = 'orange', linewidth = 1.5, label = '%D')
ax2.axhline(70, color = 'black', linewidth = 1, linestyle = '--')
ax2.axhline(30, color = 'black', linewidth = 1, linestyle = '--')
ax2.set_title(f'AAPL STOCH 14,3')
ax2.legend()
plt.show()# POSITION

position = []
for i in range(len(bb_stoch_signal)):
    if bb_stoch_signal[i] > 1:
        position.append(0)
    else:
        position.append(1)

for i in range(len(aapl['close'])):
    if bb_stoch_signal[i] == 1:
        position[i] = 1
    elif bb_stoch_signal[i] == -1:
        position[i] = 0
    else:
        position[i] = position[i-1]

k = aapl['%k']
d = aapl['%d']
upper_bb = aapl['upper_bb'] 
lower_bb = aapl['lower_bb']
close_price = aapl['close']
bb_stoch_signal = pd.DataFrame(bb_stoch_signal).rename(columns = {0:'bb_stoch_signal'}).set_index(aapl.index)
position = pd.DataFrame(position).rename(columns = {0:'bb_stoch_position'}).set_index(aapl.index)

frames = [close_price, k, d, upper_bb, lower_bb, bb_stoch_signal, position]
strategy = pd.concat(frames, join = 'inner', axis = 1)

strategy.tail()

# BACKTESTING

aapl_ret = pd.DataFrame(np.diff(aapl['close'])).rename(columns = {0:'returns'})
bb_stoch_strategy_ret = []

for i in range(len(aapl_ret)):
    returns = aapl_ret['returns'][i]*strategy['bb_stoch_position'][i]
    bb_stoch_strategy_ret.append(returns)

bb_stoch_strategy_ret_df = pd.DataFrame(bb_stoch_strategy_ret).rename(columns = {0:'bb_stoch_returns'})
investment_value = 100000
number_of_stocks = floor(investment_value/aapl['close'][0])
bb_stoch_investment_ret = []

for i in range(len(bb_stoch_strategy_ret_df['bb_stoch_returns'])):
    returns = number_of_stocks*bb_stoch_strategy_ret_df['bb_stoch_returns'][i]
    bb_stoch_investment_ret.append(returns)

bb_stoch_investment_ret_df = pd.DataFrame(bb_stoch_investment_ret).rename(columns = {0:'investment_returns'})
total_investment_ret = round(sum(bb_stoch_investment_ret_df['investment_returns']), 2)
profit_percentage = floor((total_investment_ret/investment_value)*100)
print(cl('Profit gained from the BB STOCH strategy by investing $100k in AAPL : {}'.format(total_investment_ret), attrs = ['bold']))
print(cl('Profit percentage of the BB STOCH strategy : {}%'.format(profit_percentage), attrs = ['bold']))

# SPY ETF COMPARISON

def get_benchmark(start_date, investment_value):
    spy = get_historical_data('SPY', start_date)['close']
    benchmark = pd.DataFrame(np.diff(spy)).rename(columns = {0:'benchmark_returns'})

    investment_value = investment_value
    number_of_stocks = floor(investment_value/spy[0])
    benchmark_investment_ret = []

    for i in range(len(benchmark['benchmark_returns'])):
        returns = number_of_stocks*benchmark['benchmark_returns'][i]
        benchmark_investment_ret.append(returns)

    benchmark_investment_ret_df = pd.DataFrame(benchmark_investment_ret).rename(columns = {0:'investment_returns'})
    return benchmark_investment_ret_df

benchmark = get_benchmark('2010-01-01', 100000)
investment_value = 100000
total_benchmark_investment_ret = round(sum(benchmark['investment_returns']), 2)
benchmark_profit_percentage = floor((total_benchmark_investment_ret/investment_value)*100)
print(cl('Benchmark profit by investing $100k : {}'.format(total_benchmark_investment_ret), attrs = ['bold']))
print(cl('Benchmark Profit percentage : {}%'.format(benchmark_profit_percentage), attrs = ['bold']))
print(cl('BB STOCH Strategy profit is {}% higher than the Benchmark Profit'.format(profit_percentage - benchmark_profit_percentage), attrs = ['bold']))
```