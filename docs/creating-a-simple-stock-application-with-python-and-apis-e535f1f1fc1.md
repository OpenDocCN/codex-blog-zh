# 用 Python 和 API 创建简单的股票应用程序

> 原文：<https://medium.com/codex/creating-a-simple-stock-application-with-python-and-apis-e535f1f1fc1?source=collection_archive---------4----------------------->

## 一个简单而有趣的项目，用 Python 可以在一天内完成

![](img/80d9ce3fcab730bf08325a39a09e754d.png)

克里斯汀·休姆在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

我在学习编码时明白的最重要的事情是，除了项目，没有什么能教会你你需要知道的真实世界的东西。项目是编程最有趣的方面，因为你可以创造你想象的东西。从现在起，我已经在股票市场呆了一段时间，自从我知道 Python 之后，我开始创建许多基于股票的编程项目。

在本文中，我将向您介绍我的一个最简单而有趣的股票市场项目，它利用 EOD 历史数据公司(EODhd)提供的搜索 API 来构建一个搜索应用程序，并允许用户搜索不同的股票以及其他类型的资产。事不宜迟，让我们进入文章吧！

> **注**:本文强烈基于 [EOD 史料](https://eodhistoricaldata.com/?utm_source=medium&utm_medium=post&utm_campaign=creating_a_simple_stock_application_with_python_and_apis)提供的 API。因此，为了顺利地跟进即将到来的内容，请确保您拥有自己的帐户，因为它使您能够访问您的私有 API 密钥。您可以在 处使用 [**链接创建账户。此外，并非本文中提到的所有 API 都是免费提供的，但需要订阅。点击**](https://eodhistoricaldata.com/register?utm_source=medium&utm_medium=post&utm_campaign=creating_a_simple_stock_application_with_python_and_apis) 查看所有套餐的价格，并选择最适合您的套餐。

# 用 Python 构建应用程序

## 1.导入包

让我们首先将所需的包导入到我们的 python 环境中，因为这是任何编程项目中最重要的过程。本文中我们需要的唯一包是进行 API 调用的请求和处理数据的 Pandas。如果您尚未安装这两个软件包，请在命令行中输入以下代码行:

```
pip install requests
pip install pandas
```

安装完包后，我们现在可以使用下面的代码将它们导入到我们的 python 环境中:

```
import requests
import pandas as pdapi_key = 'YOUR API KEY'
```

现在，我们已经成功地将基本包导入到我们的环境中，并将 API 键存储到`api_key`变量中。

## 2.创建搜索功能

在这一步中，我们将定义一个函数，该函数根据给定的输入搜索资产，并返回一个数据帧，该数据帧包含资产的搜索结果以及其他市场信息。该函数听起来可能包含很多功能，但可以很容易地用 Python 编程，代码如下所示:

```
def search_asset(asset, search_input, api_key):
    url = f'https://eodhistoricaldata.com/api/search/{search_input}?api_token={api_key}&type={asset}'
    results_json = requests.get(url).json()
    results_df = pd.DataFrame(results_json).drop('ISIN', axis = 1).rename(columns = {'Code':'Symbol'})

    return results_df
```

现在让我们分解上面显示的代码片段。我们要做的第一件事是定义一个名为`search_asset`的函数，它将资产的类型(`asset`)、我们想要运行搜索的资产的名称(`search_input`)和 API 键(`api_key`)作为参数。

在函数内部，我们首先将 API URL 存储到`url`变量中，并使用请求包提供的`get`函数，我们进行 API 调用来提取数据。在继续下一步之前，让我们花点时间看看 API URL 并分析它的结构:

```
https://eodhistoricaldata.com/api/search/{search_input}?api_token={api_key}&type={asset}
```

在上面的 URL 中，我们首先要看的是跟在`/search`后面的`query_string`，在这里我们必须指定我们想要搜索的资产的名称，它可以是公司名称或股票代码。

接下来，有两个参数:第一个是放入 API 键的`api_token`参数，第二个是`type`参数，其中我们必须提到我们正在寻找的资产类型(可用资产:股票、ETF、基金、债券、指数、商品、加密货币)。除了这里使用的参数之外，还有几个不同的参数可用于进一步过滤数据，但这两个参数对于我们的应用来说已经足够了。

回到代码，在进行 API 调用并存储提取的数据后，我们将 JSON 数据转换成 Pandas 数据帧，以使其更有意义，我们还做了一些数据操作来清理它，最后，我们返回数据帧。

## 3.提取输入并显示结果

这个项目的下一步也是最后一步是从用户那里获得必要的输入，并使用这些输入到我们之前创建的函数中，以显示最终结果。对于这个应用程序，我们将提取两个不同的输入:资产的类型(股票、ETF、债券、加密货币等。)，以及要搜索的资产的名称。

现在，在 Python 中有几种不同的方式从用户那里获得输入，但最基本和有效的方式是利用 Python 中的内置特性`input`函数。为我们的应用程序获取正确输入并显示最终结果的代码如下所示:

```
assets = pd.DataFrame(pd.Series(['stock', 'etf', 'fund', 'bonds', 'index', 'commodity', 'crypto'])).rename(columns = {0:'asset'})
asset = input('Type of asset [stock, etf, fund, bonds, index, commodity, crypto]: ')

if len(assets[assets.asset == asset]) == 0:
    print(cl('Input Error: Asset not found', color = 'red'))
else:
    search_input = input(f'Search {asset}: ')
    print(cl(f'\nSearch Results for {search_input}', attrs = ['bold']), '\n')
    search_results = search_asset(asset, search_input, api_key)
    display(search_results)
```

在上面的代码中，我们首先创建一个包含所有可用资产的 dataframe。接下来，我们创建一个输入字段，使用内置的`input`函数获取用户正在寻找的资产类型，从用户那里提取的输入将被存储到`asset`变量中。

然后，我们创建一个 if 语句，如果用户输入了随机的东西或不可用的资产，它会抛出一条错误消息。但是当提取的输入看起来不错时，if 语句将继续从用户那里获取下一种类型的输入，即他们想要运行搜索的资产名称。

提取所需的输入后，我们将这些输入到我们之前创建的`search_asset`函数中以运行搜索，最终结果存储在`search_results`变量中，然后使用 Pandas 包提供的`display`表示数据帧。下面是一个使用我们创建的应用程序搜索股票的例子:

作者提供的视频

# 结束语

在本文中，我们了解了如何使用 EODhd 的 Search API 构建一个简单的搜索应用程序，并且还有很大的改进空间。

一种有趣的方法是，使用我们在本文前面创建的搜索功能作为基本功能，根据输入运行搜索，并使用 EODhd 的实时资产 API 以及一些有趣的图表和视觉效果来表示实时数据，并最终将其转化为仪表板。

本文中需要注意的最重要的一点是，用 Python 构建整个应用程序至少需要 25 行代码，这主要是因为 EODhd 提供的搜索 API 简化了提取搜索结果的过程。如果没有这些，我们将不得不徒劳地经历更多的编程步骤，但是由于现有的强大 API，我们现在不需要这些了。

话虽如此，你已经到了文章的结尾。希望你学到了新的有用的东西。此外，如果您想查看原始文档以了解更多信息，请使用以下链接查看:[https://eodhistoricaldata . com/financial-APIs/search-API-for-stocks-ETFs-mutual-funds-and-indexes/](https://eodhistoricaldata.com/financial-apis/calendar-upcoming-earnings-ipos-and-splits?utm_source=medium&utm_medium=post&utm_campaign=creating_a_simple_stock_application_with_python_and_apis)