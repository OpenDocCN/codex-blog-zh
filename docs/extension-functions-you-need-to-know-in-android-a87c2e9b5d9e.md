# Android 中你需要知道的扩展功能

> 原文：<https://medium.com/codex/extension-functions-you-need-to-know-in-android-a87c2e9b5d9e?source=collection_archive---------2----------------------->

## 利用科特林的力量

![](img/5ea0e183ad78ad7655b941c4c032c8e5.png)

照片由 [Pixzolo 摄影](https://unsplash.com/@pixzolo?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Kotlin 带来了许多令人难以置信的功能。在许多不同类型的函数中，我们有**扩展函数**。它们在后面被编译成常规的 Java 函数；然而，它使编码界面看起来干净了许多。

# #0 —祝酒词

这是最简单的扩展函数，每篇文章都会告诉你要有这个函数。

现在，您可以在您的活动或片段中使用这个函数来更容易地显示祝酒词。

# #1 —重复字符串

另一个没有多少人知道的非常常见的`operator`扩展函数。

既然我们已经解决了这两个常见的问题，让我们进入一些有趣的问题。

# #2 —字符串子字符串

我相信你知道如何使用`str[index]`从字符串中获取一个字符。现在，如果您需要一个子串，但是不愿意输入长函数名，该怎么办呢？

我用一个 IntRange 参数重载了`get`操作符函数，该参数将返回所需索引范围内的子字符串。

# # 3——安卓资源

如果您需要 Kotlin 代码中的任何资源，这是一个简单的函数。删除您的样板代码，并调用您的扩展函数以获得更清晰的代码。出于调试目的，使用合适的注释，以便在传递错误的资源类型时得到提醒。

# #4 —复杂单元

你需要通过编程来设置字体大小，或者框的宽度，但是根据`dp`和`sp`？在 Java 中，你宁愿使用函数；然而，在 Kotlin 中，您可以创建一个扩展**值**。

# 结论

每个 Android 开发者都喜欢 Kotlin，只有我们充分发挥它的潜力，它才有意义。Kotlin 是一种不断发展的语言，随着每次更新，它只会变得更好。

我希望你喜欢读我的文章，并学到了一些东西。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```