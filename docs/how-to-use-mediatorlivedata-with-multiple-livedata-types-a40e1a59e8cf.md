# 如何将 MediatorLiveData 与多种 LiveData 类型一起使用

> 原文：<https://medium.com/codex/how-to-use-mediatorlivedata-with-multiple-livedata-types-a40e1a59e8cf?source=collection_archive---------0----------------------->

## 将多个 LiveData 源合并为一个

![](img/240611d7caff9691f0983e22af26f595.png)

照片由 [Pexels](https://www.pexels.com/de-de/foto/verschiedene-silberfarbene-taschenuhr-lot-selective-focus-photo-859895/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Giallo](https://www.pexels.com/de-de/@giallo?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

T he *LiveData* 组件是安卓 Jetpack 的[部分，如今被广泛用于实现观察者模式，同时自动考虑各个安卓应用的](https://developer.android.com/topic/libraries/architecture/livedata)[生命周期](https://developer.android.com/guide/components/activities/activity-lifecycle#:~:text=to%20App%20Architecture.-,Activity%2Dlifecycle%20concepts,activity%20enters%20a%20new%20state.)。

它主要用于用各个*视图模型*的新状态更新 UI…