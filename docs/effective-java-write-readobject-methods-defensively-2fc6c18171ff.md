# 有效的 Java:防御性地编写 readObject 方法

> 原文：<https://medium.com/codex/effective-java-write-readobject-methods-defensively-2fc6c18171ff?source=collection_archive---------6----------------------->

![](img/9f522223a9653a99eb7e773baf41f549.png)

照片由[思想目录](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在[的前一项](https://blog.devgenius.io/effective-java-make-defensive-copies-when-necessary-1adc245dea16)中，讨论了日期范围类。它包括`Date`字段，并小心避免打破其开始日期必须在结束日期之前的不变量。它实现这一点的方式是通过仔细编码它的构造函数和它的访问器。让我们重新熟悉一下这个类:

```
public final class Period {
  private final…
```