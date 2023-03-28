# WTF JavaScript:一个有副作用的变量

> 原文：<https://medium.com/codex/wtf-javascript-a-variable-with-side-effects-e1f44d2efc06?source=collection_archive---------16----------------------->

![](img/02cc1286329b72b6d5f8df9c6588a2bc.png)

JavaScript 有许多古怪和难以理解的行为。还有像 [WTFJS](https://www.youtube.com/watch?v=et8xNAc2ic8) 和 [WAT](https://www.destroyallsoftware.com/talks/wat) 这样的爆笑言论，取笑 JavaScript 的怪异之处。

因为我从事重构工作，所以我不得不考虑 JavaScript 的许多怪异甚至有趣的方面。我将分享我遇到的一些令人惊讶的行为，所以如果你看到了它们(希望你永远不会看到)，它可能会暗示正在发生什么。

这篇博文探讨了一个看似无辜的说法:

```
// ... some codehelloWorld;
```

您可能会直觉地认为这里不应该发生任何事情——该语句似乎什么都不做，因为这里没有明显的函数调用。

然而，它可以做任何事情，例如，打印“Hello World！”到控制台。

```
// ... some codehelloWorld;           // prints "Hello World!"
```

这怎么可能呢？

原来全局变量(在本例中是`helloWorld`)是[全局对象](https://developer.mozilla.org/en-US/docs/Glossary/Global_object)的属性。和属性不必是简单的值。它们可以使用能够执行代码的[getter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get)和[setter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set)来实现。因为在现代 JavaScript 引擎中，全局对象继承自`Object.prototype`，所以向`Object.prototype`添加属性会在全局对象上创建属性。

在这里，`helloWorld`被定义为这样一个吸气剂:

```
Object.defineProperty(Object.prototype, 'helloWorld', {
  get() { console.log("Hello World!"); },
});helloWorld;           // prints "Hello World!"
```

幸运的是，这只会发生在全局变量上，而不会发生在参数或局部变量上。

```
Object.defineProperty(Object.prototype, 'helloWorld', {
  get() { console.log("Hello World!"); },
});const a = helloWorld; // getter is evaluated, prints "Hello World!"
a;                    // does nothing
```

我在一篇关于 `[globalThis](https://mathiasbynens.be/notes/globalthis)` [聚划算](https://mathiasbynens.be/notes/globalthis)的[博文中了解到了这种奇怪的行为。在 polyfill 中，全局属性 getter 用于在不同的环境(如浏览器、web workers、框架、Node.js 和 es 模块)中访问全局`this`对象。想了解更多就看看吧！](https://mathiasbynens.be/notes/globalthis)