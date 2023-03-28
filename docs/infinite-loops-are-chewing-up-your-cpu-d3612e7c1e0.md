# 无限循环正在吞噬你的 CPU！

> 原文：<https://medium.com/codex/infinite-loops-are-chewing-up-your-cpu-d3612e7c1e0?source=collection_archive---------6----------------------->

无限循环是一种不断重复的条件循环，永远不会达到终止条件。虽然有些无限循环是错误编码的，但它们在程序需要连续运行的情况下非常有用，比如服务器程序需要一直与客户端程序通信。本文将从以下几个方面进行介绍:

```
[Infinite Loops by **Mistake**](#aa39)
[**Necessary** Infinite Loops](#e19f)
[**Stack Overflow**](#cbc5)
[**CPU Usage** of Infinite Loop](#236e)
[Use Python to Check CPU & Memory Usage](#d75a)
```

## 错误地无限循环