# 让我们制作一个 Swift 属性包装器，在编码/解码时忽略变量

> 原文：<https://medium.com/codex/lets-make-a-swift-property-wrapper-that-ignores-variables-when-en-decoding-7a2e270519eb?source=collection_archive---------3----------------------->

在这篇文章中，我们创建了一个属性包装器，可以用来在编码或解码对象时“跳过”属性。当我们想给一个不需要编码的对象添加属性时，这是很有用的，特别是当这些属性本身不可编码时。

![](img/d1e17daed245e5cc72563ce1f13022b4.png)

## 可编码协议和 Swift 的自动一致性综合

让我们首先回顾一下可编码协议的基础，看看 Swift 综合符合该协议的能力。

假设您正在 Swift 中定义一个模型:

```
**struct** MyModel {
   **let** id: Int
   **let** name: String
}
```

然后，您决定这个模型应该是“可编码的”——您希望能够将其编码到*编码器*中，或者从*解码器中解码。*幸运的是，Swift 非常聪明，让这一切变得非常简单。你所要做的就是声明这个模型是可编码的:

```
**struct** MyModel: Codable {
   **let** id: Int
   **let** name: String
}
```

Swift 已经为我们综合了协议的实现，因为结构中的每个字段都已经符合 Codable。

## 添加一个不可编码的属性，以及我们为什么要跳过它

Swift 综合符合可编码协议的能力，除了简单地在结构名后添加`: Codable`之外，我们无需编写任何代码，这非常方便，但它也有一个警告。结构或类*中的每个属性必须*本身是可编码的。如果我们添加了一个不可编码的属性，我们就失去了神奇的一致性。

让我们现在就开始吧:

```
**struct** MyModel: Codable {
   **let** id: Int
   **let** name: String

   /// Cached formatted name
   **var** formattedName: NSAttributedString? = nil
}
```

`NSAttributedString`不符合`Codable`，所以 Swift 无法再合成一致性。整个结构亮起来，显示错误“类型‘my model’不符合协议‘Codable’”。

如果我们想在这个模型上持久化所有数据，我们必须为我们的结构实现协议存根(即`encode(to:)`和`init(from:)`)。在这样做的时候，即使我们只添加了一个不可编码的字段，我们现在负责编码和解码每一个字段。

然而，仔细看看我们刚刚做出的改变，我们可能会意识到一些事情:我们实际上并不想坚持新的领域！我们只是想在显示模型时，用它作为缓存来存储一些格式化的文本。如果我们能以某种方式告诉 Swift，这个字段在对对象进行编码时并不重要——它应该被跳过——那么我们可能会重新获得自动合成。

那么，我们的目标是能够像这样声明我们的模型，并让 Swift 自动为我们处理编码和解码:

```
**struct** MyModel: Codable {
   **let** id: Int
   **let** name: String

   /// Cached formatted name
   @NotCoded **var** formattedName: NSAttributedString
}
```

## 实施时间

我们如何实现这一点？首先，它显然将是一个[属性包装器](https://docs.swift.org/swift-book/LanguageGuide/Properties.html)。此外，要实现这一点，包装的属性必须符合 Codable，这样 Swift 就能够为任何具有 NotCoded 属性的结构合成协议一致性。重要的是，当我们为属性包装器实现一致性时，我们实际上并不关心实现；我们基本上对 Swift 撒谎，说一个`NotCoded`属性是可编码的，然后实际上让它不做任何编码或解码工作。

但是这提出了一个问题:如果我们实际上不打算解码任何东西，那么当解码时，`NotCoded`属性的值应该是什么呢？我们需要某种默认值。在 Swift 中做到这一点最简单的方法是默认使用`nil`，这就是我们在这里要做的，但是如果你的项目中已经有了类似于`DefaultInitable`的协议，允许你在一致性实例上调用`init()`，你可以在其中工作。

现在是实施的时候了:

```
/// A property wrapper for properties of a type that should be "skipped" when the type is encoded or decoded.**@propertyWrapper
public** **struct** NotCoded<Value> {
  **private** **var** value: Value? **public** **init**(wrappedValue: Value?) {
    **self**.value = wrappedValue
  } **public** **var** wrappedValue: Value? {
    **get** { value }
    **set** { **self**.value = newValue }
  }
}**extension** NotCoded: Codable {
  **public** **func** encode(to encoder: Encoder) **throws** {
    // Skip encoding the wrapped value.
  } **public** **init**(from decoder: Decoder) **throws** {
    // The wrapped value is simply initialised to nil when decoded.
    **self**.value = **nil** }
}
```

由于我们用`nil`表示未解码的字段，我们必须对我们的模型结构做一个小小的改变；缓存的值是可选的:

```
**struct** MyModel: Codable {
   **let** id: Int
   **let** name: String

   /// Cached formatted name
   @NotCoded **var** formattedName: NSAttributedString?
}
```

## 我们还可以用这个模式做什么？

我们可以使用这种模式将一个字段标记为“跳过”，这样 Swift 就可以为我们的类型合成协议一致性。除了可编码之外，我们还可以用可散列的(例如，如果一个结构包含本身不可散列的字段)、可等价的等等来实现。

但是除了保留自动协议一致性合成，我们还可以使用这种模式来改变代码的行为。考虑一个非散列属性包装，其工作原理与非编码相同。当然，我们可以包装*任何*属性来跳过散列，包括那些会修改对象散列的属性。假设我们有这样一个模型:

```
struct BookModel {
  let id: Int
  let name: String
  let rating: Int
}
```

根据我们的使用情况，我们可能不希望在散列时包括一本书的评级。例如，如你所知，当一个类型是可散列的，它也是等价的。如果我们想检查两本书是否相同，比如说，在一个数组中查找重复的内容，我们不希望 ID 和名称相同的两本书因为评级不同而被认为不同。

因此，一旦我们基于上面演示的模式定义了我们的`NotHashed`包装器，我们就可以修改我们的模型，如下所示:

```
struct BookModel: Hashable {
  let id: Int
  let name: String
  @NotHashed let rating: Int
}
```

显然你在做这样的事情时应该小心；也许可以添加清晰的文档，解释一些字段被排除在对象的散列之外。但是如果你在散列、编码、解码等时需要跳过字段，希望这种模式能让你的生活更轻松。