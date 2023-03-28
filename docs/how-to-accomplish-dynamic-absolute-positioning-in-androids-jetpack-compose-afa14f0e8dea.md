# 如何在 Android 的 Jetpack Compose 中实现动态绝对定位

> 原文：<https://medium.com/codex/how-to-accomplish-dynamic-absolute-positioning-in-androids-jetpack-compose-afa14f0e8dea?source=collection_archive---------5----------------------->

## 通过使用其他元素作为锚点，在组件父级的精确坐标内定位组件

![](img/ca8776ab774aa912d6d216cec385721b.png)

图片由 [cottonbro](https://www.pexels.com/de-de/@cottonbro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 在 [Pexels](https://www.pexels.com/de-de/foto/blond-gesicht-portrat-weiblich-8090149/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 上拍摄

当使用 Jetpack Compose 时，在大多数情况下，你只需要用普通的方式在一个固定的布局结构中布局组件。