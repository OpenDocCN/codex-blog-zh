# 用 Python web 抓取纽约时报的文章:第一部分

> 原文：<https://medium.com/codex/web-scraping-the-new-york-times-articles-with-python-part-i-e2d6fc02d4e0?source=collection_archive---------2----------------------->

在数字时代，你可能会在互联网上找到大量的公共信息，你可以利用这些信息为自己谋利。您可以手动将数据复制粘贴到一个新文档中来检索这些信息，但这可能非常耗时；然而，网络抓取是在相对较短的时间内提取*大量数据的理想方式。*

*![](img/fbfede67c3f7c1b6ef234ebcc2b0ec7d.png)*

*照片由[莎拉·舒尔](https://unsplash.com/@sarahshull?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/the-new-york-times?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄*

*Web 抓取是使用软件程序从网站获取数据的自动 ***过程，该软件程序通过手动利用 HTTP 协议或在应用程序中集成浏览器来复制人类 web 导航。****

*Web 抓取被广泛使用，并且具有多种应用:*

*营销和销售公司可以通过使用 web 抓取来定位其受众或跟踪行业趋势，从而获得与销售线索相关的信息。*

*网络搜集对于寻找新项目、转售房产等信息的房地产公司很有帮助。*

*Trivago 等价格比较公司严重依赖网络抓取从各种电子商务网站收集产品和价格信息，以便为客户提供最佳酒店价格。*

*网络抓取 Twitter 数据通常用于在即将到来的选举中产生有见地的信息。*

*求职者可以从各种网站自动抓取招聘信息，并将其整合到一个数据源中。*

**是不是很神奇？**

*本帖将是 ***3 系列*** 的第一部分。我将带您了解如何从《纽约时报》收集文章，对它们进行预处理，删除标点符号和停用词，以及应用标记化、词条化和搭配。最后，我们将使用 ***LDA*** 进行主题发现，使用 ***TextBlob*** 进行情感分析。*

*在这篇文章中，我将提供关于如何抓取纽约时报文章的说明。 为了能够使用他们的 API，你需要按照下面的说明创建一个开发者账户:*

*[https://developer.nytimes.com/get-started](https://developer.nytimes.com/get-started)*

*注册帐户时，必须注册一个应用程序，并选择要激活的 API。一些选项是文章搜索 API、热门故事 API 和存档 API，但也包括电影评论或书籍 API。您可以启用多个。一旦您创建了一个应用程序，您将获得您的 API 密钥，您将使用它与您选择的 API 进行交互。*

*要了解如何请求文章搜索 API，您可以查看概述:*

*[https://developer . nytimes . com/docs/articlesearch-product/1/overview](https://developer.nytimes.com/docs/articlesearch-product/1/overview)*

*开始吧！*

```
*import requests as req
import time
API_KEY='YOR_API_KEY' # your API key
TOPIC='Technology' # keyword*
```

*在文章搜索概述中，您可以找到可用主题的列表。
其中包括艺术、商业、教育、科学、时尚、健康、政治等等。*

*url 调用示例:*

```
*https://api.nytimes.com/svc/search/v2/articlesearch.json?q=technology&api-key=yourkey*
```

*如果你想搜索多个关键词，你可以使用 news_desk。
FQ = news _ desk:(“Technology”“Science”)用于技术和科学文章。
除此之外，你还可以使用诸如出版日期、出版年份、来源、材料类型和星期几等字段来限制你的文章。*

*文章搜索 API 一次最多返回 10 个结果。请记住，每个 API 有一个请求限制:每分钟 10 个请求，每天 4000 个请求。
这里我们收集了 1200 篇文章，并将使用 time.sleep()来避免达到 API 的每分钟请求限制。sleep 函数在给定的秒数内暂停执行。*

```
*for i in range(120):
  url='[https://api.nytimes.com/svc/search/v2/articlesearch.json?q='+TOPIC+'&api-key='+API_KEY+'&page='+str(i)](https://api.nytimes.com/svc/search/v2/articlesearch.json?q='+TOPIC+'&api-key='+API_KEY+'&page='+str(i))
  response = req.get(url).json()
  time.sleep(6)
response*
```

*我们的响应是 JSON 格式的，看起来像这样:*

```
*{'status': 'OK',
 'copyright': 'Copyright (c) 2022 The New York Times Company. All Rights Reserved.',
 '***response***': {'docs': [{'***abstract***': 'Concerns that popular social media platforms can expose children to posts that are sexualized, hurt their body image or are violent have escalated in recent years.',
    'web_url': '[https://www.nytimes.com/2022/03/29/technology/snapchat-tiktok-parental-control.html](https://www.nytimes.com/2022/03/29/technology/snapchat-tiktok-parental-control.html)',
    'snippet': 'Concerns that popular social media platforms can expose children to posts that are sexualized, hurt their body image or are violent have escalated in recent years.',
    '***lead_paragraph***': 'A group of attorneys general on Tuesday asked Snap and TikTok to work more closely with parental control apps and to apply more scrutiny to inappropriate content on their platforms, the latest salvo in a growing fight over child protection between governments and social media companies.',
    'source': 'The New York Times',
    'multimedia': [],
    '***headline***': {'***main***': 'State attorneys general ask Snap and TikTok to give parents more control over apps.',
     'kicker': None,
     'content_kicker': None*
```

*仔细查看回复，我们将提取摘要、标题和 lead_paragraph(我只是为了更好地可视化而突出显示了它们)并创建一个文章列表。*

```
*# Extract the necessary fields from the response.
articles = []
docs = response['response']['docs']
for doc in docs:
        filteredDoc = {}
        filteredDoc['title'] = doc['headline']['main']
        filteredDoc['abstract'] = doc['abstract']
        filteredDoc['paragraph']=doc['lead_paragraph']
        articles.append(filteredDoc)
articles[:10]*
```

*这里我们可以看到输出的样子:*

```
*[{'title': 'State attorneys general ask Snap and TikTok to give parents more control over apps.',
  'abstract': 'Concerns that popular social media platforms can expose children to posts that are sexualized, hurt their body image or are violent have escalated in recent years.',
  'paragraph': 'A group of attorneys general on Tuesday asked Snap and TikTok to work more closely with parental control apps and to apply more scrutiny to inappropriate content on their platforms, the latest salvo in a growing fight over child protection between governments and social media companies.'},
 {'title': 'Ben McKenzie Would Like a Word With the Crypto Bros',
  'abstract': 'The actor, best known for his starring role in “The O.C.,” has become an outspoken critic of a volatile market driven by speculation. Who’s listening?',
  'paragraph': 'ROCKDALE, Texas — Ben McKenzie was driving his father’s silver Subaru through Texas farmland, talking in breathless bursts about money: who has it, who needs it, what makes it real or fake. He detailed the perils of cryptocurrency exchanges, the online brokers that sell Bitcoin and Ether to speculators, then delivered a glowing endorsement of “Capital in the Twenty-First Century,” a 700-page book by the economist Thomas Piketty about income inequality and the power of wealthy capitalists.'},
 {'title': 'How Robots Can Assist Students With Disabilities',
  'abstract': 'New tools use artificial intelligence to assist students with autism and dyslexia and address accessibility for those who are blind or deaf.',
  'paragraph': 'This article is part of a limited series on how artificial intelligence has the potential to solve everyday problems.'},*
```

*瞧啊。既然我们已经使用纽约时报 API 收集了文章，我们将创建一个熊猫数据帧，并将其保存为 CSV 文件以供以后使用。*

```
*import pandas as pd
df = pd.DataFrame(data=articles)
df.to_csv('TechArticles.csv')*
```

*在下一篇文章中，我们将讨论数据预处理。我们将移除停用词，并应用标记化、词条化和搭配来为主题发现和情感分析做准备。*

## *结论*

*Web 抓取是一种从网站自动收集数据的有效方法。网络抓取可以跟踪价格、产生线索、改进营销技术、自动提取图像等。然而，你必须谨慎行事，以避免沉溺于不道德的行为。*

*参考资料:*

*[纽约时报开发者网络](https://developer.nytimes.com/)*

*Jupyter 笔记本的详细代码可以在这里找到:*

*[https://github . com/sivosevic/NYTimesNLP/blob/main/nytimestechdatacollection . ipynb](https://github.com/sivosevic/NYTimesNLP/blob/main/NYTimesTechDataCollection.ipynb)*

*如果您觉得这篇文章内容丰富、引人入胜，请务必关注我，以获得关于未来文章和教程的通知。*

*你可以查看我的其他作品:[https://medium.com/@eellaaivo](/@eellaaivo)*

*感谢阅读，敬请期待[的下一篇文章！](/@eellaaivo/analyzing-the-new-york-times-articles-with-python-part-ii-text-data-preprocessing-a-1cf7425acb86)*