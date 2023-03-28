# Java 的优点:嵌套类、内部类和匿名类的区别

> 原文：<https://medium.com/codex/finer-points-of-java-the-difference-between-nested-inner-and-anonymous-classes-2c7ca0ac4f60?source=collection_archive---------8----------------------->

![](img/c8365da7ee34c1bc4c312be68de44791.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的 [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 拍摄

一个 Java 项目通常有几个类，每个类都在自己的源文件中单独定义。不过，有时将一个 Java 类放在另一个中会很方便。这里有一个玩具例子:

```
package org.example;public class EnclosingClass { class EnclosedClass {
```