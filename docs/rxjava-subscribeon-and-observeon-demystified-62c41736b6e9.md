# RxJava 订阅和观察去神秘化

> 原文：<https://medium.com/codex/rxjava-subscribeon-and-observeon-demystified-62c41736b6e9?source=collection_archive---------8----------------------->

![](img/193686c0c3df46175682cab2091f347e.png)

[博欣·卡拉伊万诺夫](https://unsplash.com/@bkaraivanov?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](/s/photos/thread?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## [法典](http://medium.com/codex)

在使用 RxJava 进行 api 调用时，我们通常会编写以下代码:

```
api.getData("2487956")
        .subscribeOn(Schedulers.*io*())
        .observeOn(AndroidSchedulers.*mainThread*())
        .subscribe()
```

我们使用了 RxJava 中两个最常用的线程操作符`subscribeOn`和`observeOn`。让我们探索一下…