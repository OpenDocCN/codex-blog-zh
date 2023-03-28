# 我已经受够了这该死的编程语言中该死的不可变元组

> 原文：<https://medium.com/codex/ive-had-it-with-these-motherf-king-immutable-tuples-in-this-motherf-king-programming-language-7e0e24a1dbfc?source=collection_archive---------6----------------------->

![](img/e938339529e38f00b12c8a7ee285b724.png)

丹尼尔·埃勒杜特在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

所以在 Dart 中，我一直使用这个[包](https://pub.dev/packages/tuple)来获取元组。你可以在这里找到[的源代码。](https://github.com/google/tuple.dart/blob/master/lib/tuple.dart)

我将把您的注意力引向这里的一小段代码:

```
/// Represents a 2-tuple, or pair.
class Tuple2<T1, T2> {
  /// Returns the first item of the tuple
  final T1 item1;
```