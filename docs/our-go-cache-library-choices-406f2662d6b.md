# 我们的 Go 缓存库选择

> 原文：<https://medium.com/codex/our-go-cache-library-choices-406f2662d6b?source=collection_archive---------0----------------------->

## 各方面对比 golang bigcache、go-cache、groupcache、golang-lru

![](img/2d69494cae25119f3003e97a56b6f969.png)

来自 unsplash， [@kaitlynraeann](https://unsplash.com/photos/BKNFx3nUHQo)

在 [*用 20 分钟从零开始构建 Go KV 缓存*](https://blog.devgenius.io/build-a-go-kv-cache-from-scratch-in-20-minutes-a13616a4929f) 中，我向您展示了编写本地缓存时的重要事项，并最终实现了一个缓存，尽管它的性能被 Github 上流行的 [go 缓存](https://github.com/patrickmn/go-cache)所击败。然而，好的一面是我们可以从…中学到很多东西