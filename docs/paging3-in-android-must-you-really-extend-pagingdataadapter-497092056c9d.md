# Android 中的 Paging3，真的必须扩展 PagingDataAdapter 吗？

> 原文：<https://medium.com/codex/paging3-in-android-must-you-really-extend-pagingdataadapter-497092056c9d?source=collection_archive---------7----------------------->

当使用 Android 分页库时，你的适配器类必须从`PagingDataAdapter`扩展还是可以扩展通常的`RecyclerView**.**Adapter`或其子类？请继续阅读，了解如何以更灵活的方式使用分页库。

![](img/2ebace2d9ec691547cffc0df46e785d3.png)

# 为什么首先要使用分页 3