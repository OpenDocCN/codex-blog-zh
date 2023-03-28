# Kotlin 1.5 内联类——几乎没有成本地提高代码的可维护性

> 原文：<https://medium.com/codex/kotlin-1-5-inline-classes-improve-code-maintability-at-almost-no-cost-9fd10f1668c4?source=collection_archive---------4----------------------->

## 新包装类简介

![](img/1954f62c63250cbbeb7e5c0a40fc6ae6.png)

照片来自 [Pixabay](https://www.pexels.com/de-de/@pixabay?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 由 [Pexels](https://www.pexels.com/de-de/foto/beleuchtete-ringe-am-strassenrand-290470/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

K otlin 内联类是 [Kotlin 值类](https://github.com/Kotlin/KEEP/blob/master/notes/value-classes.md)的子集。内联类可以作为某种其他类型的包装器，在很高的层次上进行描述。