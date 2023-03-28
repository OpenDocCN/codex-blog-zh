# Javascript 承诺:顺序、并行和竞赛

> 原文：<https://medium.com/codex/promises-sequential-parallel-race-1ac18ff09f60?source=collection_archive---------4----------------------->

有很多方法可以让你从承诺中获得回报。来说说他们吧。

![](img/95083e3198b3d02c782555f06ad17766.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Greg Rakozy](https://unsplash.com/@grakozy?utm_source=medium&utm_medium=referral) 拍摄的照片

假设你有三个承诺要兑现。

让我们创建一个函数来创建这些承诺。

```
const createPromise = (value, delay) => {
  return new Promise((resolve, reject) => {
    setTimeout(()=> {
      resolve(value)
    }, delay)…
```