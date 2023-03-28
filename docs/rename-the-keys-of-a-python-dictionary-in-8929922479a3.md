# python 3.7 中 dict 实现的变化

> 原文：<https://medium.com/codex/rename-the-keys-of-a-python-dictionary-in-8929922479a3?source=collection_archive---------5----------------------->

## 重命名 Python 字典的所有关键字， [CODEX](http://medium.com/codex)

## 如何在 python 3.7 及以上版本中修改字典的键而不改变其值

![](img/b9f9ecdb0e4299483b84cffefa0d3ccf.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 拍摄的照片

**注:本文还有**[**https://dineshkumarkb . com/tech/change-python-dictionary-keys-without-changing-its-values/**](https://dineshkumarkb.com/tech/change-python-dictionary-keys-without-changing-its-values/)

在本文中，我们将研究如何通过利用 python 3.7 中的字典实现变化来改变字典的键而不改变其值。

从 python 3.7 和更高版本开始，字典将按照 [python-dev](https://mail.python.org/pipermail/python-dev/2017-December/151283.html) 邮件列表保留插入顺序。字典顺序保证是插入顺序。这种行为是 CPython 3.6 的一个实现细节。

让我们利用上述行为来重命名这些键，如下所示。

## Python 3.7 及以上版本

```
>>> new_keys = [**"**user_name**"**,**"**user_id**"**,**"**user_email**"**]
>>> old_dict = {**"**name**"**:**"**testname**"**,
            **"**id**"**:123,
            **"**email**"**:**"**test@example.com**"**}

>>> print(dict(zip(new_keys,old_dict.values())))Output:
{'user_name': 'testname', 'user_id': 123, 'user_email': '[test@example.com](mailto:test@example.com)'}
```

## 这里发生了什么？

在上面的输出中，所有的键都被重命名，同时保留了值。

old_dict.values()按照插入顺序提供 old_dict 字典中的值列表，即姓名、id 和电子邮件。

我们使用 zip 函数将旧字典中的值与 new_keys 列表中的键合并，new _ keys 列表返回一个 zip 对象，最终可以转换为 dict。

[https://docs.python.org/3/library/functions.html#zip](https://docs.python.org/3/library/functions.html#zip)

> 注意:上述代码也适用于低于 3.7 的 python 版本。然而，字典条目的检索顺序是任意的。所以秩序无法保证。

在 python 3.7 和更高版本中，由于插入顺序被保留，我们可以保证插入顺序。