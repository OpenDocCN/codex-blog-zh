# 如何在 Python 3 中有效地打开文件

> 原文：<https://medium.com/codex/how-to-open-files-effectively-in-python-4d269720f684?source=collection_archive---------18----------------------->

## open()函数和上下文块的初学者指南。

![](img/f6a045dc2b690a50cb079efe569e4145.png)

照片由[克里斯里德](https://unsplash.com/@cdr6934?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

Python 3 提供了一个内置函数，可用于打开文件:

`open("readme.txt")`

这种方法是读取文本文件最简单、最简单的方法:

```
my_file_handler = open("readme.txt", "r")
my_file =…
```