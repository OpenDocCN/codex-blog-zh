# 构建新蛋网刮刀(第 1 部分)

> 原文：<https://medium.com/codex/building-a-newegg-web-scraper-part-1-3e6671ee24ff?source=collection_archive---------2----------------------->

## [法典](http://medium.com/codex)

## 下载适合工作的工具

![](img/78154816ea19f3a1a42a294e5fc207bc.png)

安德烈·季霍诺夫斯基在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 什么是 web scraper？

我们可以编写程序来读取某些类型的文件。这些可以包括文本(。txt)、逗号分隔值(。csv)和图像文件(。jpg，。png，。bmp)(仅举几例)。然而，web scraper 是一个从网站读取 HTML 代码的应用程序。HTML 解析库可以用来以优雅的方式与网站源代码进行交互。

# 我建造的刮刀的描述

我构建的 web scraper 基于 YouTube 上的一个教程视频(该视频将在文章底部引用)。该视频侧重于构建一个 web scraper 来从 Newegg 网站获取信息。从图形卡中检索品牌名称、产品名称和发货等信息。我还想增加从每个显卡上抓取价格信息的功能。这一小小的改变导致了对原始代码的实质性修改。获取价格信息迫使我不得不考虑缺货的商品。我还必须添加代码来解释广告。如果我不检查广告，那么我的刮刀获取信息的能力就会减弱。在本系列的下一部分中，我将提供关于这些部分的更多细节。

![](img/ab4acbc78a6755811d8d0f4e17ebf512.png)

照片由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [NeONBRAND](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 拍摄

# 此构建所需的工具

*   计算机编程语言
*   点
*   美味的汤

Python 是一种高级编程语言(这只是意味着它有类似英语的语法)。你想要构建的东西取决于你使用的语言。有些语言用于构建特定的事物，有些语言可以用于构建各种各样的事物。似乎有无数基于 Python 的 web 抓取库可供选择。因此，在构建 web scraper 时，Python 是首选语言。

Pip 是一个 Python 包管理器。编程语言有许多标准库，它们构成了与语言整体相关的特性。标准库是可以直接访问的库(通过使用某种关键字将库包含在项目中)和间接访问的库(通过默认情况下包含在项目中的库)。不属于核心语言的库需要单独下载，以便您可以在项目中使用它们。Pip 允许我们下载不属于 Python 编程语言的外部库。

Beautiful Soup 是一个基于 Python 的库，用于 web 抓取(它更正式的名称是 HTML 解析器)。有很多库可以用来进行网络抓取，但是，似乎有很多资源可以利用 Beautiful Soup。如果您想使用另一个库，如 Selenium 或 Scrapy，那么我们非常欢迎您。

![](img/fef8dcf6c7371f41f38faea52d90e814.png)

詹姆斯·哈里逊在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 下载 Python

获取最新版本的 [Python](https://www.python.org/downloads/) 。如果您在 Windows 机器上，那么您必须将核心 Python 脚本的位置添加到 path 环境变量中。如果不这样做，需要从命令提示符执行的命令将不起作用。

# 正在下载画中画

将 [get-pip.py](https://bootstrap.pypa.io/get-pip.py) 保存到你的电脑上。使用终端将您的目录设置为包含 get-pip.py 文件的目录。完成后，在终端中输入以下命令:

```
py get-pip.py
```

上面的行将执行文件中的代码，并将 pip 安装到您的计算机上。如果上面的代码行不通，你也可以用“python”代替“py”。我的系统允许我通过在目标文件前写“py”来执行 Python 代码。无论什么对你的系统起作用，那就去做吧。

# 下载美丽的汤

将这行代码写入您的终端以安装美丽的汤:

```
pip install bs4
```

您也可以查看 [Beautiful Soup 文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)了解更多安装信息。当你在网站上，向下滚动到“安装美丽的汤。”

# 参考

数据科学道场。(2017 年 1 月 6 日)。介绍用 Python 和漂亮的汤进行网络抓取。【YouTube 视频】。数据科学道场。从[https://www.youtube.com/watch?v=XQgXKtPSzUI&t = 205s](https://www.youtube.com/watch?v=XQgXKtPSzUI&t=205s)取回