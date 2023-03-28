# 搜索引擎优化的 HTML 属性和标签

> 原文：<https://medium.com/codex/html-attributes-and-tags-for-seo-448bce3a2fc0?source=collection_archive---------18----------------------->

![](img/2fef5a6bca3c83ddf605b149211d5b0c.png)

内森·达席尔瓦在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这是一个正在进行的关于 SEO 的系列，点击这里查看[第一部分](/@langatlangs/what-is-seo-2d2e93d75c9)和[第二部分](/codex/on-page-seo-c3faddba9ea)。

*本文假设读者了解 HTML。*

## 介绍

当用户在谷歌这样的搜索引擎中搜索某样东西时，它会努力给出所能找到的最佳内容。谷歌通过抓取网站和查看所有的 HTML 文档来做到这一点。引擎试图通过查看一些东西来理解网站的内容。其中包括内容关键短语、内容的结构、内容的长度等等。

当搜索 HTML 时，搜索引擎会对你的内容进行分类。如果 HTML 的结构和优化做得很好，你的网站将会在搜索结果中被返回。在本文中，我们将研究 HTML 标签及其属性。

## 标题标签和属性

当用户在搜索后得到结果时，出现在搜索引擎结果页面(SERP)中的大部分是内容的标题。因此，优化标题标签<title>并将其定位到内容的关键词是非常关键的。</title>

除了标题标签，另一个关键部分是标题属性。

```
<p><abbr title=”Search Engine Results Pages”>SERPs</abbr> In the phrase above</p>
```

在这种情况下，属性是附加到 Title 标签上的一条信息，它给出了关于标题的更多信息。当鼠标悬停在标题上时，此信息通常显示为工具提示。

这个属性可以用来让标题更加清晰，搜索引擎会为此感谢你的。

你应该小心不要使用误导性的标题或点击诱饵，因为搜索引擎已经变得非常聪明，会惩罚这种行为。文章的标题应该与内容相关，因为这将确保你的内容与用户的搜索查询相关。

## 标题

# 至

标题标签用于组织内容，以方便用户阅读。内容应该有条理，例如，有引言、正文和结论。

这使得它更容易理解，并有利于愉快的阅读。搜索引擎使用这些标签来确定搜索结果中的内容片段。

## 描述

这是由标签表示的元数据。这不会出现在页面上，但会被浏览器用于页面重新加载或如何显示页面，以及被搜索引擎用于对页面进行分类。

```
<meta name="description" content="SEO HTML Tags and Attributes">
```

建议在这里使用你的关键字，这将有助于搜索引擎对你的网页内容进行排名。

## 图像属性

图像是用户寻找的信息的一部分。因此，如果图像能够包含更多的信息，这将是很有帮助的。

```
<img src="[https://cdn-images-1.medium.com/max/800/0*snNMU-IezkxFki6E](https://cdn-images-1.medium.com/max/800/0*snNMU-IezkxFki6E)" alt="HTML SEO">
```

标签携带的两个属性是指向存储的“src”和给出图像更多信息的“alt”。它指定了图像的用途，并在搜索时增加了曝光度。

## 不跟随

链接在网站中至关重要，使用时要小心。在 HTML 中，元素指向一个链接，从中可以找到更多的信息。rel 属性指定当前网页和目标链接之间的关系。

```
<a rel="nofollow" href="https://twitter.com/Dev_Langs/">Not relevant</a>
```

谷歌确实会抓取这些链接，如果发现不相关，就会惩罚该网页。因此，使用 rel 属性的 nofollow 值通知搜索引擎不要跟踪该链接是很重要的。

可用于忽略链接的其他 rel 值包括“赞助商”，用于赞助商链接，以及“ugc”，用于到用户生成内容的链接。

## 机器人

正如在[的上一篇文章](/@langatlangs/what-is-seo-2d2e93d75c9#:~:text=as%20featured%20snippets.-,Robots.txt,-Search%20engines%20use)中所解释的，robots.txt 文件用于通知网络爬虫他们可以访问哪些 URL。您应该使用它来确保您的站点不会因请求而过载。

```
<meta name="robots" content="noindex" />
```

这通过不显示结果中指定的页面来减轻网络爬虫的负载。如果您想要寻址一个特定的 crawler，您可以用您想要寻址的特定 crawler 替换值“robots”。这些特定的爬虫被称为用户代理，[谷歌的用户代理之一被称为 Googlebot，你只需替换名称值。](https://developers.google.com/search/docs/advanced/crawling/overview-google-crawlers)

```
<meta name="googlebot" content="noindex" />
```

## 规范链接<link rel="”canonical”">

当你在一个以上的网页上有重复的信息时，通知搜索引擎将来自 URL 的信息合并成一个 URL 是很重要的。这个 URL 将是权威的，规范属性有助于将页面排名整合到您首选的权威 URL。

这个标签必须在 HTML 文档的 部分。

```
<Head><link rel="canonical" href="http://example.com/"></Head>
```

重复可能是由多种因素造成的，包括不同的主机/协议可访问性。其他原因是由于多个 URL、内容管理系统或网站印刷版本(例如移动和桌面版本)等。

## 视口

搜索引擎将拥有最佳用户体验的网页排在比不具备最佳用户体验的网页更靠前的位置。用户体验的一部分是能够在任何设备上访问网页。因此，网页应该对所有设备都友好。在 HTML 中确保这一点的方法是使用 viewport meta 标记。

```
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

如果你的网页不具备这种设备友好性，它就不会对你的页面进行很好的排名。

## 打开图形标签

社交媒体是一个巨大的信息分享和消费平台。因此，毫不奇怪，搜索引擎在排名时会考虑这个因素。Open Graph 是一种脸书协议，它使网页能够成为社交图中的丰富对象。

这仅仅意味着这个协议控制链接在社交媒体中被消费时的显示方式。它们被社交媒体平台所使用，并在分享时使网页可点击。Open Graph 标签位于网页的 header 部分，由 og: meta 属性表示。

```
<meta property="og:title" content="html attributes and tags for seo" /><meta property="og:type" content="article" /><meta property="og:url" content="[https://www.medium.com/](/@langatlangs/html-attributes-and-tags-for-seo-448bce3a2fc0)" /><meta property="og:image" content="[https://ia.media-imdb.com/images/rock.jpg](https://cdn-images-1.medium.com/max/800/0*snNMU-IezkxFki6E)" />
```

Twitter 也有卡片标签，这是它自己的平台，它的工作方式类似于 Facebooks Open Graphs。唯一不同的是，Twitter 的卡是专门在 Twitter 中使用的。

```
<meta name="twitter:card" content="summary" />
```

## 结论

HTML 标签和属性，作为页面搜索引擎优化策略的一部分，和技术搜索引擎优化一起，都被用来向更多的观众展示高质量的内容。为了达到这个结果，重要的是有效地实施所有这些策略，避免仅仅为了排名而填充它们。同样重要的是要知道搜索引擎会变得越来越聪明，这些策略会随着时间的推移而演变。

搜索引擎优化有令人兴奋的新发展，例如，机器学习的使用。谷歌已经开始实施机器学习来过滤搜索结果，通过使用一种算法，称为 RankBrain 算法。这些发展使得这个领域不断进化，为了竞争，你必须适应和进化。