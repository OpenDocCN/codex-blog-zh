# InnerHTML vs. DOM 速度:我错了，对吗？

> 原文：<https://medium.com/codex/innerhtml-vs-dom-speed-i-was-wrong-and-right-41d0e06f2b3b?source=collection_archive---------0----------------------->

![](img/798bf2d5c1248d77a36035eaa34ec192.png)

不久前[Marian aikovski](https://medium.com/u/a87faeb8b9d?source=post_page-----41d0e06f2b3b--------------------------------)写了一篇非常好的文章“[插入 DOM 哪个更快——HTML 还是 DOM 节点？](/codex/what-is-faster-to-insert-into-dom-html-or-dom-nodes-ff11586f8570)“问题是，给出的数字让 innerHTML 和 append 在性能上看起来几乎一样，这就是我所知道的关于使用 DOM utter 和完全胡说八道的一切。这让我质疑基准测试方法的缺陷。