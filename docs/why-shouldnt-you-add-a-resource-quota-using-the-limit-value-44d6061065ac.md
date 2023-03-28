# 为什么不应该使用限制值添加资源配额？

> 原文：<https://medium.com/codex/why-shouldnt-you-add-a-resource-quota-using-the-limit-value-44d6061065ac?source=collection_archive---------24----------------------->

## 如果不小心，可能会阻碍工作负载的可伸缩性。

![](img/91d5ce530dc041c34a234666fdbd183d.png)

Joshua Hoehne 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

基于容器的开发的一大好处是能够定义隔离空间，在这些空间中，您可以保证 CPU 和内存等资源。这也在基于 Kubernetes 的环境中在名称空间级别上得到扩展，所以…