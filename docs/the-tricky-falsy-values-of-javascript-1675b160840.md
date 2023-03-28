# Javascript 微妙的“虚假”值

> 原文：<https://medium.com/codex/the-tricky-falsy-values-of-javascript-1675b160840?source=collection_archive---------19----------------------->

## 因为在 Javascript 中设置默认值看起来非常简单……直到你意识到哪里出错了！

![](img/0996ed5ca28501867041eac0061d78ff.png)

当你写代码时，你希望你的变量有明确定义的值——你想知道在你程序中的任何给定点它们等于什么。简单的情况是当你显式设置一个变量时:如果你说`a` …