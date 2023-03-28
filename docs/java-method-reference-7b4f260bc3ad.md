# Java 方法参考

> 原文：<https://medium.com/codex/java-method-reference-7b4f260bc3ad?source=collection_archive---------9----------------------->

## 方法引用允许您使用现有方法作为 lambda，只要它们的签名(输入和输出)兼容。

我最早是从 C#学的方法引用，所以 Java 的概念我很熟悉。或者这就是我的想法，直到我拿起[现代 Java 食谱书](https://www.oreilly.com/library/view/modern-java-recipes/9781491973165/)。

在本文中，我们将探索 Java 中 3 种形式的方法引用

*   `object::instanceMethod`
*   `Class::staticMethod`
*   `Class::instanceMethod`