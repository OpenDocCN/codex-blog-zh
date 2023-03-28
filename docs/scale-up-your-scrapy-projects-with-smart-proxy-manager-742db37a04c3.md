# 使用智能代理管理器扩展您的零碎项目

> 原文：<https://medium.com/codex/scale-up-your-scrapy-projects-with-smart-proxy-manager-742db37a04c3?source=collection_archive---------17----------------------->

![](img/2043a03110b9f772222b6c00ab92fd41.png)

在本教程中，你将学习如何扩大你已经存在的 Scrapy 项目，以便提出更多的请求和提取更多的网络数据。

Scrapy 是一个非常流行的网页抓取框架，如果你是一个网页数据提取开发者，它会让你的生活变得更加容易。Scrapy 可以处理许多网络抓取工作，包括 URL 发现、解析、数据清理、自定义数据管道等……但有一件事 Scrapy 不能开箱即用，如果你想可靠地提取大量数据，它已经成为一个必须的:代理管理。

为了扩大你的 Scrapy 项目，你需要一个代理解决方案。

我会告诉你如何把你已经存在的 Scrapy 蜘蛛，并推动它与代理人！

# 入门指南

在这个例子中，我将使用包含两个功能的产品提取蜘蛛的“Scrapy 版本”:

1.  寻找产品网址的爬虫
2.  一个真正提取数据的刮刀

以下是 Scrapy 蜘蛛代码:

```
class ProductSpider(CrawlSpider): 
name = 'product' 
start_urls = ['http://books.toscrape.com/catalogue/category/books/travel_2/index.html'] rules = ( Rule(LinkExtractor(restrict_css='article.product_pod > h3 > a'), 
callback='populate_item'), 
) def populate_item(self, response): 
item = ProductItem() 
book_title = response.css('div.product_main > h1::text').get() price_text = response.css('p.price_color::text').get() 
stock_info = response.css('p.availability').get() 
item = { 
'title': book_title, 
'price': self.clean_price(price_text), 
'stock': self.clean_stock(stock_info) 
} 
yield item def clean_price(self, price_text): 
return Price.fromstring(price_text).amount_float def clean_stock(self, stock_info): 
return remove_tags(stock_info).strip()
```

因此，让我们假设这是你目前拥有的蜘蛛，它工作得很好…为你提供宝贵的数据…一段时间。然后，您希望扩大规模并提出更多请求，最终导致阻塞、低成功率、差的数据质量等…

克服障碍和接收高质量 web 数据的解决方案是代理以及如何使用这些代理。让我们看看如何在这个蜘蛛中集成智能代理管理器！

# Scrapy +智能代理管理器(以前的 Crawlera)

推荐的集成方式是[官方中间件](https://github.com/scrapy-plugins/scrapy-zyte-smartproxy)。您可以这样安装它:

```
pip install scrapy-crawlera
```

然后，将这些设置添加到您的剪贴簿项目文件中:

```
DOWNLOADER_MIDDLEWARES = {'scrapy_crawlera.CrawleraMiddleware': 610} CRAWLERA_ENABLED = True CRAWLERA_APIKEY = '<API key>'
```

请注意，为了使用智能代理管理器，您需要一个 API 密钥。但是不要担心，我们提供了一个 [14 天的免费试用](https://www.zyte.com/smart-proxy-manager/?utm_campaign=SPMEU&utm_activity=BLO&utm_medium=ORG&utm_content=ScaleScrapySPMblog&utm_primary=SPM&utm_goal=SIN)(马克斯·10K 在试用期间请求)，所以你可以快速获得你的 API 密钥，并试用它以确保它为你工作。

或者，如果您请求智能代理管理器的自定义实例，也可以设置代理 URL:

```
CRAWLERA_URL = 'myinstance.zyte.com:8011' Another way to set up Smart Proxy Manager is directly in the spider, like this: class ProductSpider(CrawlSpider): 
crawlera_enabled = True 
crawlera_apikey = 'apikey'
```

# 设置建议

为了在使用智能代理管理器时获得更高的爬网率，我们建议禁用自动节流插件并增加最大并发请求数。您可能还想增加下载超时时间。以下是实现该目的的设置列表:

```
CONCURRENT_REQUESTS = 32 
CONCURRENT_REQUESTS_PER_DOMAIN = 32 
AUTOTHROTTLE_ENABLED = False 
DOWNLOAD_TIMEOUT = 600
```

# 注册智能代理管理器帐户

这个简单的集成处理了你用代理扩展你的 Scrapy 项目所需要的一切。

如果您厌倦了阻塞或管理不同的代理提供者，请尝试智能代理管理器(以前的 Crawlera)。

*最初发表于*[T5【https://www.zyte.com】](https://www.zyte.com/blog/scale-up-your-scrapy-projects-with-zyte-smart-proxy-manager-formerly-crawlera/)*。*