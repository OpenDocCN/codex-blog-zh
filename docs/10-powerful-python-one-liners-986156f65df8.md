# 10 个强大的 Python 一行程序

> 原文：<https://medium.com/codex/10-powerful-python-one-liners-986156f65df8?source=collection_archive---------20----------------------->

一行程序通常更节省时间。它们实现了相同的目的，并有助于提高代码的可读性，而不是编写一整块代码。

# For 循环

## 1.用于序列创建的简单 for 循环

例如，要创建 0 到 5(包括 0 和 5)的列表:

```
lst = []for i in range(5+1): # i starts with 0 and ends with 5
    lst.append(i)
```