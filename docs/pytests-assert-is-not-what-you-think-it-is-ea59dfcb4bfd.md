# Pytest 的断言不是你所想的那样

> 原文：<https://medium.com/codex/pytests-assert-is-not-what-you-think-it-is-ea59dfcb4bfd?source=collection_archive---------3----------------------->

![](img/402a5a37baa89037a35565a8a94c9b8b.png)

达米安·帕特考斯基在 [Unsplash](https://unsplash.com/s/photos/africa?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

## AST 是什么？pytest 如何破解它，给你一个更好的 UX？

在 Python 中，像许多其他语言一样，有一个检查给定条件的语句，如果该条件为*假*，则引发 ***AssertionError*** ，否则，如果给定条件为*真*，则不执行任何操作。这基本上就是 ***断言*** 做的事情。