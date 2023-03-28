# 操作系统中的死锁|解释

> 原文：<https://medium.com/codex/deadlock-in-operating-system-explained-3585ef77e161?source=collection_archive---------15----------------------->

一个 [*死锁*](https://www.mayhemcode.com/2021/07/deadlock-in-operating-system-explained.html) 是死锁中的所有进程都被对方阻塞或者通过持有资源来等待对方的状态。

![](img/b5bd6aedee55c0086c27496d1a4ab5e8.png)

我们来看看为什么会出现死锁，死锁的条件是什么，如何预防。

# 什么是死锁？

在操作系统中，资源可能不是**无限的**或者一次只能被一个进程使用。为了提高进程的速度，一些进程在获得 CPU 周期之前持有资源，并且还需要一些正在执行的其他进程。但是这里发生了悲剧性的变化，如果执行中的进程也需要一些被前一个进程占用的进程呢？在这里，我们可以看到一个锁，它是在它们如何相互等待的进程和 CPU 仍然空闲的进程之间创建的。这个进程有时会导致严重的问题，当它们无限期地占用资源时，可能会导致等待其他进程。在现实生活中可以看到一个经典的例子，比如道路交通，车辆从两侧驶来，但当车辆同时从两侧驶来时，两辆车都将卡在路上，并阻挡其他车辆。

![](img/731ecc513da76f97fc33171cc1f6e72c.png)

如今，操作系统能够在主存中存储多个进程。因此，这些进程甚至在获得 CPU 周期之前就持有它们的资源，这可能会导致死锁。此外，如果没有办法抢占进程，解决死锁会变得更加困难。让我们来看看一个系统是否陷入死锁的必要条件。有 **4 种方式来识别**这个。

有关死锁处理和条件的更多信息，请访问

[https://www . mayhemcode . com/2021/07/deadlock-in-operating-system-explained . html](https://www.mayhemcode.com/2021/07/deadlock-in-operating-system-explained.html)

*原载于 2021 年 7 月 13 日 https://mayhemcode.blogspot.com*[](https://mayhemcode.blogspot.com/2021/07/deadlock-in-operating-system-explained.html)**。**