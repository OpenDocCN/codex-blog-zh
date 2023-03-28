# 导航 Etsy API

> 原文：<https://medium.com/codex/navigating-the-etsy-api-6f7f3abc739b?source=collection_archive---------8----------------------->

![](img/11921b12272c83e4f3ace8b07e955d03.png)

[法托斯 Bytyqi](https://unsplash.com/@fatosi?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我最近完成了一个需要使用 Etsy API 的项目，并且非常兴奋地发现了这样一个记录良好且有用的 API。希望会有更多的项目使用这个单一来源的广泛数据。

首先，让我们了解一下在 python 中使用 API 的基本知识。为了从 API 导入数据，我们将使用 GET 请求和 API URL。我喜欢将 GET 请求的参数作为独立变量。

然后，我们将检查响应状态代码，以确保请求是…嗯，正确请求的。除了 200 状态代码之外的任何代码都需要进行一些调查。完整的状态代码列表可以在[这里](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)找到。

```
url = 'https://openapi.etsy.com/v2/listings/active?'
params = {'api_key': 'YOUR API KEY'}response = requests.get(url = url, params = params)response.status_code
```

Etsy 的响应是 JSON 格式的，所以要查看数据，我们需要使用。json()方法。

```
etsy_data = response.json()
```

太好了！在更具体地谈论 Etsy 的 API 之前，这些是我们需要了解的基础知识——所以让我们开始吧！

在直接进入 API 之前，一定要先阅读文档。Etsy 的很全面，很有帮助。基本文档[链接于此](https://www.etsy.com/developers/documentation/getting_started/api_basics)。这包括数据类型、参数选项、获取 API 键的指令等等。在这个页面的顶部，所有 API 的引用都是链接的。这些引用允许用户提取某些信息。

假设我们想要查看杯垫的当前产品列表，我们将使用带参数的[“列表”引用](https://www.etsy.com/developers/documentation/reference/listing)来缩小杯垫的搜索范围。每个引用的文档中都包含可用的字段，我们将使用 tags 字段作为参数来查找杯垫，并将返回的列表限制为 5 个。

```
params = {'api_key': 'YOUR API KEY', 'tags': ['coaster'], 'limit': 5}response = requests.get(url = url, params = params)
etsy_listings = reponse.json()
etsy_listings.keys()*dict_keys(['count', 'results', 'params', 'type', 'pagination'])*
```

我们看到我们有一个包含计数、结果、参数、类型和分页键的字典。请注意，调用“结果”会给我们留下一个列表。对列表进行索引将为我们获得每个列表的字典。让我们来看看。

```
results = etsy_listings['results']
first_listing = results[0]
first_listing.keys()*dict_keys(['listing_id', 'state', 'user_id', 'category_id', 'title', 'description', 'creation_tsz', 'ending_tsz', 'original_creation_tsz', 'last_modified_tsz', 'price', 'currency_code', 'quantity', 'sku', 'tags', 'materials', 'shop_section_id', 'featured_rank', 'state_tsz', 'url', 'views', 'num_favorers', 'shipping_template_id', 'processing_min', 'processing_max', 'who_made', 'is_supply', 'when_made', 'item_weight', 'item_weight_unit', 'item_length', 'item_width', 'item_height', 'item_dimensions_unit', 'is_private', 'recipient', 'occasion', 'style', 'non_taxable', 'is_customizable', 'is_digital', 'file_data', 'should_auto_renew', 'language', 'has_variations', 'taxonomy_id', 'taxonomy_path', 'used_manufacturer', 'is_vintage'])*
```

我们可以看到我们的结果包含了大量的信息，从产品的价格到是否是古董。从这里开始，世界就是你的了！我喜欢选择我想要使用的键，并将它们放入数据框中，以便于清理和可视化——但是选项是无穷无尽的。

这篇简短的博客只涵盖了 Etsy 的许多参考资料中的一个，不要忘记访问他们的 API 文档并看看其他的。它们包括图像、用户资料和地址，以及支付和运输信息。

感谢阅读！希望这给了你一个很好的想法，如何轻松地通过一个 API。