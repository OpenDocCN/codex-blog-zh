# 在 C#中迭代动态对象

> 原文：<https://medium.com/codex/iterating-through-a-dynamic-object-in-c-a3c604141569?source=collection_archive---------4----------------------->

![](img/7800c53f5192b764a57857f3f9df11a8.png)

I 如果我们事先知道对象的结构，遍历对象以获得其实际值相对容易，但是对于动态生成的动态对象，我们还必须了解它们的动态结构。

有时候，对象是通过反序列化 JSON 字符串构建的，例如，这些对象可以具有任何结构，属性值可以是字符串、数字、其他对象或数组，我们必须首先识别属性值类型，然后才对其应用相关操作:解析字符串/整数/浮点值或继续迭代。

首先，让我们创建一个复杂的 JSON 对象结构，我们将使用 Newtonsoft.json NuGet 包将它转换成一个动态对象。如果你不熟悉在 C#中使用 JSON，请查看[这篇文章](https://turmanauli.medium.com/a-complete-guide-for-serializing-json-to-dynamic-objects-on-the-fly-in-c-7ab4799f648d)。

下面是一个我们将要转换的 JSON 字符串的例子:

这是一个 quant 机器人建立买入头寸的标准列表。

我们从一个**和**属性开始，该属性的值是一个列出标准的数组，该数组中列出的所有标准都应该满足才能开仓，然而，有些项目不仅仅是字符串，它们还是具有 or 属性的其他对象，该属性的值也是一个列出标准的数组。至少要满足这些标准中的一个，才能开立买入头寸。

概括地说，逻辑的基本部分存储为字符串，它们可以是 AND 或 or 数组的一部分，并且所有或其中一个数组项应该分别得到满足，以便打开一个位置。

现在让我们将 JSON 字符串转换成一个对象:

我们可以验证是否所有内容都被正确解析:

> `Generated Object: {
> “AND”: [
> “d1==false”,
> “d3==false”,
> {
> “OR”: [
> “d4==false”,
> “d2==false”
> ]
> },
> “b1>=b2*1.6”,
> “b2>=b3*0.7”,
> “l1<=ma”,
> {
> “OR”: [
> “lt1==0”,
> “b1/lt1<=28”
> ]
> },
> “h1<daily_h1”,
> “daily_l1<daily_c2”,
> “h1<h2”
> ]
> }`

似乎一切都很好，我们得到了和开始时完全一样的值。

现在我们需要了解我们通常会遇到哪些类型的数据，这些数据是简单的变量、对象或数组，所以方法应该是这样的:

*   取一个变量并检查它的类型。
*   如果类型是对象，则循环访问其属性。
*   如果类型是数组，则迭代其值。
*   如果类型是字符串/数字，则处理它。

这个函数可能需要自己执行，这取决于它遇到了多少层嵌套逻辑。

让我们创建一个函数，精确地告诉我们给定变量的类型，在 C#中有 3 种方法来检查变量类型:

*   可变。GetType()
*   类型(变量)
*   是

typeof(variable)实际上给出了你在声明变量、对象或数组时指出的类型，我们没有那样做，所以在这种情况下它不会工作。

至于变量。GetType()，它应该可以工作，我们来看看它做了什么:

下面是上面代码的精确输出:

> `Newtonsoft.Json.Linq.JObject
> Newtonsoft.Json.Linq.JArray
> Newtonsoft.Json.Linq.JValue`

因此，Newtonsoft.json 只使用 JObject、JArray 和 JValue 类型构建了一个动态对象，很高兴知道这一点，让我们继续吧…

P.S. **is** 在这种情况下也不会起作用，让我们检查所有可能的情况，只是为了确定:

输出:

> `a1 ok`
> `b1 ok`
> 

让我们开始创建一个迭代函数，首先，我们需要识别变量的类型:

这个代码

将输出以下内容:

> `type is Object`
> `type is Array`
> `type is Variable`
> 

现在我们需要遍历一个数组或对象:

输出:

> `type is Object
> property name: AND
> property type: Newtonsoft.Json.Linq.JProperty
> type is Array
> type is Variable, value: d1==false
> type is Variable, value: d3==false
> type is Object
> property name: OR
> property type: Newtonsoft.Json.Linq.JProperty
> type is Array
> type is Variable, value: d4==false
> type is Variable, value: d2==false
> type is Variable, value: b1>=b2*1.6
> type is Variable, value: b2>=b3*0.7
> type is Variable, value: l1<=ma
> type is Object
> property name: OR
> property type: Newtonsoft.Json.Linq.JProperty
> type is Array
> type is Variable, value: lt1==0
> type is Variable, value: b1/lt1<=28
> type is Variable, value: h1<daily_h1
> type is Variable, value: daily_l1<daily_c2
> type is Variable, value: h1<h2`

现在，您可以根据需要处理变量类型了！

感谢您的阅读！

如果你喜欢这篇文章，请点击那个按钮，这样更多的人将能够找到它！

你想知道我是做什么的吗？我是 Proxify Network 的高级软件开发人员。

现在 Proxify 正在寻找新的开发人员，所以我想在这里分享一些我的经历——与我在职业生涯中尝试的其他事情相比:
1️⃣友好和专业的 Proxify 团队以及他们对每个候选人的关注程度
2️⃣招聘过程非常简单，可以充分展示你的技能
3️⃣，最重要的是，Proxify 是寻找远程工作的理想方式，有无穷无尽的项目和公司可供选择。

在此申请加入我在 https://bit.ly/3hd64mN[的代理公司](https://bit.ly/3hd64mN)

你还想要额外的免费资料吗？

【https://m.do.co/c/88f25eea9442】点击此链接注册，即可从数字海洋获得 100 美元的云积分

![](img/a56fdc2d417997653d3ea19c3ded5b83.png)

[**【140 美元来自 FBS**](https://fbs.com/promo/trade-100usd?ppu=193551) **:** 这家经纪公司受 IFSC 监管，是历史最悠久、成立最久的机构之一，自 2009 年开始运营。

**要求:**

*   [注册一个有 140 美元的新账户](https://fbs.com/promo/trade-100usd?ppu=193551)
*   利用 1:500 的杠杆，让你的利润最大化
*   你可以提取所有利润

**可用市场:**加密货币、股票、差价合约、金属、商品、外汇

![](img/d01d0eca0648f163c82f7759703b6df0.png)

[**【30 美元来自 Tickmill**](https://secure.tickmill.com/redirect/index.php?cii=15604&cis=1&lp=https%3A%2F%2Ftickmill.com%2Fpromotions%2Fwelcome-account%2F) :受 FSA 监管，该经纪商自 2015 年开始运营。

**要求:**

*   注册一个有 30 美元的新账户
*   使用高达 1:500 的杠杆来最大化您的利润
*   在 5 手交易后提取利润
*   最高取款金额是 300 美元

**可用市场:**股票指数，石油，贵金属，债券，外汇。

![](img/931e59f88e70d767c2f051b8e1e72e28.png)

[**【30 美元来自 Roboforex**](http://www.roboforex.com/clients/promotions/welcome-program/?a=arag) :受 CySEC 和 IFSC 监管，Roboforex 自 2009 年开始运营，是当今交易者中最受欢迎和信任的经纪商之一。

**要求:**

*   [开立账户](http://www.roboforex.com/clients/promotions/welcome-program/?a=arag)并存入 10 美元以验证您的支付方式(可随时提取)并获得 30 美元作为礼物
*   **利润可以无限制提取**
*   如果你交易了必要数量的手，你也可以提取 30 美元

**可用市场:**股票(所有纽交所、纳斯达克、美国证券交易所股票+德国和中国上市公司)、股票差价合约(所有股票差价合约)*【美国上市股票每笔交易费 1.5 美元】*、指数、ETF、商品、金属、能源商品、加密货币、加密指数、外汇。

![](img/1d2cd192c0404bd4feaa38abf1173e77.png)

[**币安的所有交易终身享受 10%的折扣:**](https://www.binance.com/en/register?ref=P5O06MBF) 币安是世界上收费最低的加密货币交易所，支持迄今为止最多样的加密交易或投资方式:

*   [现货交易](https://www.binance.com/en/register?ref=P5O06MBF)；
*   [点对点(P2P)交易](https://www.binance.com/en/register?ref=P5O06MBF)；
*   [保证金(最高 10 倍杠杆)交易](https://www.binance.com/en/register?ref=P5O06MBF)；
*   [加密期货交易](https://www.binance.com/en/register?ref=P5O06MBF)；
*   [加密转换](https://www.binance.com/en/register?ref=P5O06MBF)及更多…

当你投资任何加密货币时，你可以通过允许保证金(杠杆)交易的借贷获得额外的无风险被动收入，它带有一个强大的[在线(web)平台](https://www.binance.com/en/register?ref=P5O06MBF)、 [Windows](https://www.binance.com/en/download?ref=P5O06MBF) 、 [Mac](https://www.binance.com/en/download?ref=P5O06MBF) 、 [Linux 软件](https://www.binance.com/en/download?ref=P5O06MBF)，以及面向软件开发者的 [Android](https://www.binance.com/en/download?ref=P5O06MBF) 和 [iOS 应用](https://www.binance.com/en/download?ref=P5O06MBF) + [币安 API](https://www.binance.com/en/download?ref=P5O06MBF) 。

[币安](https://www.binance.com/en/register?ref=P5O06MBF)不仅费用最低，而且是为数不多的支持交易 **Dogecoin** 的平台之一，就交易量和允许交易的加密货币而言，是世界上最大的加密交易所。

**存款选项包括:**

*   将任何上市加密货币直接加密存入您的币安钱包；
*   使用您的信用卡/借记卡购买加密货币；
*   通过银行转账(SWIFT 或 SEN)存入 35 种不同的法定货币。

> 从 [***此链接***](https://www.binance.com/en/register?ref=P5O06MBF) ***注册后，您现在可以获得任何交易 10%的佣金返现。***

祝你有愉快的一天！