# 科特林的 5 个不为人知的特点

> 原文：<https://medium.com/codex/5-untold-features-of-kotlin-6cd623768c1e?source=collection_archive---------0----------------------->

## 科特林的隐藏特征

![](img/a8808da9b953ba5f84017de878cf9407.png)

[来源](https://wallpapercave.com/kotlin-wallpapers)

你用 Kotlin 编程多久了？如果你认为你知道 Kotlin 中所有可用的特性，那么，再想想吧。这里有 5 个你可能不知道的 Kotlin 功能。

# 解构

有些人认为析构只在一些语言中有效，比如 JavaScript 和 Python。但是这个特性在 Kotlin 中也有。

从一个对象，你可以收集它的属性；从列表中，你可以按顺序收集元素。

# 收藏 KTX

Collections KTX 是一个用于 Kotlin 扩展函数的库，非常有用。首先，在项目中安装依赖项，

```
implementation "androidx.collection:collection-ktx:1.2.0"
```

这将开启一系列全新的功能。

```
// Combine 2 ArraySets into 1.
val combinedArraySet = arraySetOf(1, 2, 3) + arraySetOf(4, 5, 6)

// Combine with numbers to create a new sets.
val newArraySet = combinedArraySet + 7 + 8
```

以上代码来自 [Android 文档](https://developer.android.com/kotlin/ktx#collection)。

# 惰性装载

延迟加载是 Kotlin 中最常用于 Android 的一种技术。程序最初启动时，不会加载该变量。相反，它将在第一次被调用时加载。

在我们的活动生命周期中，当触发`onCreate`方法时，`layoutInflater`被初始化。在外面初始化`binding`变量会导致错误。然而，当变量被延迟加载时，初始化代码仅在第一次使用时运行。

# Typealias

您是否厌倦了每次都定义相同的长数据类型？Kotlin 有一个特性可以让你的代码看起来更好，读起来也更好。

您可以用一个更好地描述代码的词来替换所有长数据类型的实例。

# 反勾号

你是否需要写一个具体的变量名，但这在 Kotlin 中是一个很难的关键字？在大多数语言中，他们禁止你这么做；但科特林没有。

这段代码*可能不被*欣赏，但是我用这个例子是为了更好的理解。用反斜线命名函数的一个好地方是当它们是**测试**函数时。

```
@Test
fun `return true if date is null`() {
  // Your assertions
}
```

# 结论

科特林永远不会让你失望。它是一种不断发展的编程语言，是你想要和需要的所有特性的完美结合。语言已经有了如此大的飞跃；未来更多的更新只会让它变得更好。

我希望你喜欢读我的文章，并学到了一些东西。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```