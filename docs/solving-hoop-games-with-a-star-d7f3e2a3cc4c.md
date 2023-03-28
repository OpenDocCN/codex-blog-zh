# 用 A 星解铁环游戏

> 原文：<https://medium.com/codex/solving-hoop-games-with-a-star-d7f3e2a3cc4c?source=collection_archive---------8----------------------->

*用著名的寻路算法解决配色游戏*

我妻子最近给我看了一个[可爱的小 iPhone/iPad 游戏](https://apps.apple.com/in/app/hoop-sort-color-ring-puzzle/id1615927837)，类似于经典的[汉诺塔](https://en.wikipedia.org/wiki/Tower_of_Hanoi)逻辑谜题。这款游戏可以免费下载，而且在没有互联网连接的情况下也能运行，这意味着当你被困在没有 WiFi 的飞机上时，玩这款游戏非常棒。

像许多游戏一样，它非常简单，充满了大量的广告。与许多游戏不同，它实际上需要一些策略——你可以玩它，而没有[剥皮者盒子](https://levelskip.com/how-to/Skinners-Box-and-Video-Games)的随机性或[母牛](http://www.cowclicker.com/)的支付赢的特征…