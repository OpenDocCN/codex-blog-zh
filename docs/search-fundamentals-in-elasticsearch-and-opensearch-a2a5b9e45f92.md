# ElasticSearch 和 OpenSearch 中的搜索基础。

> 原文：<https://medium.com/codex/search-fundamentals-in-elasticsearch-and-opensearch-a2a5b9e45f92?source=collection_archive---------13----------------------->

DSL 搜索语言基础。

ElasticSearch(或 AWS 中类似的 OpenSearch)是一个全文搜索系统。给你一个提示，简单想想谷歌搜索。您可以为您的项目创建类似的东西。

> 据[wiki](https://en.wikipedia.org/wiki/Elasticsearch):
> **elastic search**是基于 [Lucene](https://en.wikipedia.org/wiki/Lucene) 库的[搜索引擎](https://en.wikipedia.org/wiki/Search_engine_(computing))。它提供了一个分布式、[、支持](https://en.wikipedia.org/wiki/Multitenancy)的[全文搜索](https://en.wikipedia.org/wiki/Full-text_search)引擎，带有 [HTTP](https://en.wikipedia.org/wiki/HTTP) web 界面和…