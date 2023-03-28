# 逻辑数据建模的重要性

> 原文：<https://medium.com/codex/the-importance-of-logical-data-modelling-c9686ccf4f1?source=collection_archive---------13----------------------->

![](img/43bc4975d2cecc50f4ab3cdf03a14dde.png)

结构体💥/枚举❤️

作为一名 iOS 开发人员，我总是对在产品代码中看到`fatalError(“This should never happen”)`的频率感到震惊。这通常出现在编程上可能，但逻辑上不可能的情况下。

# 场景

想象一下这样一种情况，我想要一个应用程序来对我卧室书架上的 CD 和书籍进行分类。我创建了一个`Item` struct，它告诉我标题，并有可选的持续时间和页数字段，这取决于它是在模拟 CD 还是书。

```
struct Item {
    var title: String
    var duration: TimeInterval?
    var numberOfPages: Int?
}
```

这个结构给我留下了一个问题。我可以创建一个既有`duration` *又有*和`numberOfPages`的项目。我也可以创建一个没有字段的`Item`！

当我试图将我的`Item`表示为表格中的行时，问题变得很明显:

```
func makeRow(for item: Item) -> Row {
    if let duration = item.duration {
        return Row(heading: title, subHeading: duration)
    } else if let numberOfPages = item.numberOfPages {
        return Row(heading: title, subHeading: numberOfPages)
    } else {
        fatalError(“This should never happen”)
    }
}
```

尽管有错误消息，但`fatalError` *可能会*发生。为了让我的应用程序崩溃，我只需编写以下代码:

```
let item = Item(title: “Moby-Dick”)
let row = makeRow(for item) // 💥 Crash here!
```

在这里很容易编写不重要的代码，而且仅仅从调用点来看，并不能立即看出会发生崩溃。

# 对此能做些什么呢？

任何时候，当我有两个互斥的字段，而我只需要一个字段时，我的经验法则就是去找一个`enum`！

通过利用`enum` s，我们可以以一种确保编译器只允许逻辑状态存在的方式来构造我们的代码。一旦我们消除了不可能的状态，无意义的状态就消失了！

# 第二次:拯救我的 enums！

首先，我重构了我的代码，并用下面的`enum`表示这些项目:

```
enum Item {
    case song(title: String, duration: TimeInterval)
    case book(title: String, numberOfPages: Int)
}
```

行构建代码现在也需要重构:

```
func makeRow(for item: Item) -> Row {
    switch item {
    case let .song(title, duration):
        return Row(heading: title, subHeading: duration)
    case let .book(title, numberOfPages):
        return Row(heading: title, subHeading: numberOfPages)
    }
}
```

注意`fatalError`是如何消失的！现在这里不可能有崩溃，因为不可能有无效的`Item`。

# 结论

给出的例子显然是人为的，但是架构模式是无价的。我在处理 JSON 解码的代码中最常看到这个问题。下次查看从 JSON 解码的结构时，试着找出互斥的字段。如果你最终得到一个无效的对象，很可能你的解码操作已经失败了！