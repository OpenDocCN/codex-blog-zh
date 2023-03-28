# Python 是赚钱的可靠途径(第 3 部分)

> 原文：<https://medium.com/codex/python-is-the-sure-way-to-make-money-part-3-9b340995972d?source=collection_archive---------1----------------------->

“Telegram 是 python 的最佳商业伙伴”

![](img/a0661c40b5d4091ebe03bc3e7144b414.png)

Python 作为赚钱工具越来越受欢迎。这和我们在本文第一部分 中讨论的 [**由于种种原因是一致的。**](/@lorence80/python-is-the-sure-way-to-earn-money-5c115c55ae6b) **[**第二部分**](/@lorence80/python-is-the-sure-way-to-make-money-part-2-5bc8082ccd07) 本文只有 16 行代码，演示了用 Python 抓取网站数据是多么容易。**

第三部分，也是本文的最后一部分，将讨论 Python 程序员的最佳商业伙伴，例如 Telegram。我们不必通过自由职业者网站或其他方式等待工作；相反，通过 Telegram，我们可以积极地为我们的社区创造一些服务，并获得回报。

例如，在 Telegram 中，我们可以为我们的社区创建以下服务:

a.流量报告:我们可以抓取一些实时发布流量报告的网站，转发给我们的电报机器人，然后电报机器人再转发给订阅了我们电报频道的其他人。我们可以通过偶尔发布联盟营销产品来实现电报渠道的货币化。

b.url shortener:我们可以创建 url shortener 服务，这样其他人就不必去 url shortener 网站，而是可以在我们的 Telegram 频道中发布他们的 URL，我们在 Heroku 服务器上由 Python 编程驱动的 telegram bot 将立即缩短 URL。也许我们可以免费赠送前十个网址，然后如果他们想缩短更多的网址，他们可以支付微薄的会员费，比如 1 美元。Telegram 提供了一种我们可以直接使用的支付服务，无需建立新的支付服务。

c.价格跟踪:与上面的第二个例子相似。对于我们的社区，我们可以通过电报提供价格跟踪。其他人只需要在 Telegram 中发布产品 url，Heroku 服务器中的 python 编程会跟踪产品 url，并在产品价格低于所需价格时向 Telegram 发送消息以通知该人。下面，我们将尝试构建一个简单的 Python 应用程序来演示这一点。

这次我们将使用 Selenium 包从 Amazon 网站获取价格数据。为什么是硒？因为对于 java script 生成的网站或者对数据抓取比较敏感的网站，Selenium 比 BeautifulSoup 表现更好。Selenium 使用 chromedriver，所以它将模拟自然浏览，而不是以抓取为目的的浏览。

首先，我们需要将 Selenium 包安装到 Python 环境中

然后我们将需要从 [**这里**](https://chromedriver.chromium.org/downloads) 获取 chromedriver。根据您的操作系统选择版本；对于 Windows，即使我们有 64 位系统，我们也可以使用 32 版本。

将 chromedriver.exe 放在我们虚拟环境的脚本文件夹中。因此，当我们稍后使用 Selenium 时，我们将不必指定 chromedriver 的路径。所以，我们现在已经准备好开始编写应用程序了。

首先，我们导入 Selenium 的 webdriver 和 by 函数。除此之外，我们还导入了请求包，用于与电报机器人和 Python 时间模块进行通信，以备后用。

因为我们这次只做一个基本的价格跟踪应用，输入将直接来自我们的计算机，只有输出发送到我们的电报机器人。写这篇文章的时候，我用了[https://www . Amazon . com/HAVIT-Rainbow-Backlit-Gaming-Keyboard/DP/b 016y 2 bvka/](https://www.amazon.com/HAVIT-Rainbow-Backlit-Gaming-Keyboard/dp/B016Y2BVKA/)作为产品网址。

我们将产品 URL 保存到变量 product URL 中，将我们的警报价格保存到变量 alert price 中。为了确保变量被正确填充，我们打印出 alertprice 变量。记住，对于 alertprice，我们希望得到不带$因为输入函数的返回是一个字符串，所以在与当前产品价格进行比较之前，我们必须将其转换为一个浮点数。Python 的 float 类型表示十进制数，而不是整数。

接下来，我们创建一个在 Amazon 网站上查看产品价格的函数。为什么我们需要创建一个函数？这将在本文的后半部分解释。

如果说在 [**第二部**](/@lorence80/python-is-the-sure-way-to-make-money-part-2-5bc8082ccd07) 中，BeautifulSoup 有一个在网站中查找 web 元素的 find 函数，Selenium 有 find_element 函数。在上面的例子中，我们使用 find_element by ID 来查找使用 ID 属性的 web 元素，使用 find_element by Class_Name 来查找使用 Class 属性的元素。我们使用 webdriver 的 get 函数从 Amazon 网站检索产品 URL。

如果我们想将警报发送到我们的电报机器人，我们需要将第八行的打印函数改为我们将在下面创建的 send_message 函数。我们可以稍后通过更改警报价格来尝试我们的应用程序功能，以尝试我们的应用程序是否发送通知。

我们如何获得我们的机器人令牌和聊天 ID？请参考电报网站 [**这里**](https://core.telegram.org/bots#6-botfather) 。在我们获得 Bot 令牌和聊天 ID 后，请不要忘记将我们代码的第八行改为:

为了确保我们跟踪 Amazon 是否更新了其网站上的产品价格，我们现在需要使用 while 函数不仅一次，而且定期调用我们的函数。为了避免网站负担过重，在部署期间将睡眠时间设置为 3600 秒。

在 30 行 Python 代码中，我们能够创建我们的价格跟踪应用程序，并向我们的电报机器人发送警报通知。希望这能激励我们中的一些人想出新的方法来利用 Python 和 Telegram 赚钱。完整的源代码可以在下面找到。