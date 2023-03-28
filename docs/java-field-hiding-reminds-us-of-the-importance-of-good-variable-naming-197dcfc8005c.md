# Java 字段隐藏提醒我们良好的变量命名的重要性

> 原文：<https://medium.com/codex/java-field-hiding-reminds-us-of-the-importance-of-good-variable-naming-197dcfc8005c?source=collection_archive---------6----------------------->

![](img/c46f539535c3abf4624a412bdd15d646.png)

蒂姆·谢泼德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Java 中的字段隐藏是该语言的一个有点令人困惑的特性，或者说是错误的特性，这取决于您的看法。考虑这个玩具例子:

```
public abstract class SomeSuperClass {

    **public final String something = "Just for the sake of example";**

    public void printSomethingNumberOfTimes(int number) {
        if (number > 0) {…
```