# 面向多人游戏的大规模实时消息传递——AWS 架构

> 原文：<https://medium.com/codex/massive-scale-real-time-messaging-for-multiplayer-games-20ca37ef9766?source=collection_archive---------5----------------------->

![](img/40361554db6024d0f4546e77a7a2215c.png)

图片由来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3652514) 的 [Ruwad Al Karem](https://pixabay.com/users/ruwadium-1674203/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3652514) 提供

通过使用多个 **Redis** PubSub 集群和**Amazon elastic cache**和 **WebSockets** ，向多人游戏中的玩家发送通知的实时消息，可以扩展到支持数百万并发用户(CCU)。