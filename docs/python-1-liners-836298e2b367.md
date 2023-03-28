# Python 1-liners

> 原文：<https://medium.com/codex/python-1-liners-836298e2b367?source=collection_archive---------6----------------------->

Python 如今处于食物链的顶端。使用 python，程序员只需一行代码就能完成工作。这里只是一些基本的一行程序。

![](img/f83d5cde2991142a927b623d64400f8d.png)

罗宾·皮耶尔在 [Unsplash](https://unsplash.com/s/photos/quick?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

## 交换变量

相互交换变量只需要一行代码，因为你不需要处理像内存地址这样麻烦的东西:

```
x = 10 
y = 40
x, y = y, x
**print**(x,y)
```