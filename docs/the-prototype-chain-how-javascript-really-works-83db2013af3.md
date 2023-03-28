# 原型链:JavaScript 如何真正工作

> 原文：<https://medium.com/codex/the-prototype-chain-how-javascript-really-works-83db2013af3?source=collection_archive---------0----------------------->

## [法典](http://medium.com/codex)

![](img/714c7192a3dff33dcf8493c39549d996.png)

如果像我一样，您以前的编程经验是使用面向对象的语言，如 Java 或 C#，JavaScript 的基于原型的继承方法可能需要一分钟才能让您明白。

你可以过得很好，不需要太深入研究，甚至不需要考虑原型。然而，当我们将构造函数和类混合在一起时，事情开始变得更有趣了。

所以，让我们在🤿潜水吧

# 什么是原型？

JavaScript 中的每个对象都可以链接到原型对象，原型对象是提供继承的机制。

您可以使用`__proto__`属性查看对象指向的原型。

附带说明:围绕使用`__proto__`有一些争议，实际上它被弃用，而支持`Object.getPrototypeOf()`。你可以在这里了解更多信息[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)

当在一个对象上调用一个属性或方法时，如果该对象不包含它，那么 JavaScript 引擎将查找该对象的原型来解析该属性或方法。

你可能以前见过术语**原型链**。这是对象本身的原型也可以包含原型等等的地方。

如果一个对象不包含属性或方法，JavaScript 引擎将继续沿着原型链向上尝试并解决它。

# 构造函数

你可能在 JavaScript 中看到过一些用`new`关键字调用的函数，例如`new Date()`——这些是**构造函数。**为了与本文的其余部分保持一致，下面是一个构造函数的样子以及它的用法:

```
function Swan(colour, mood) {
    this.colour = colour;
    this.mood = mood;
}let swan = new Swan('white', 'angry');
```

上面的代码块使用构造函数`Swan`创建了一个对象`swan`，属性`colour`和`mood`分别设置为`'white'`和`'angry'`。注意构造函数大写的惯例。

你可能已经注意到了`this`关键字的用法，如果你花了很多时间在 JavaScript 或其他面向对象语言上，你可能已经见过它的用法了。不过要小心，与 Java 等面向对象语言相比，它在 JavaScript 中的表现可能不像你预期的那样。

## 这

我们不会对`this`进行过多的描述，因为它是 JavaScript 中与绑定相关的最令人困惑的领域之一，很容易就有一篇专门的文章(如果您觉得这有帮助，请告诉我！).

长话短说，`this`的值可以根据函数被调用的位置而变化。

当在如上例的构造函数的上下文中使用时，JavaScript 引擎创建一个内部的`this`上下文，该上下文被绑定到使用构造函数创建的新对象上——在我们的例子中是`swan`。

# 使用原型链的继承

> 在 JavaScript 中，所有函数都有一个原型属性，所有对象都有一个指向其构造函数原型的`__proto__`属性。

可以认为`__proto__`属性指向创建它的函数的原型。

记住，我们也可以使用`Object.getPrototypeOf(yourObject)`获得对象的原型，这样做是更好的做法。

让我们更深入地研究一下前面的例子:

```
function Swan(colour, mood) {
    this.colour = colour;
    this.mood = mood;
}let swan = new Swan('white', 'angry')Swan.prototype.isSassy = () => true;
```

Swan 有一个原型属性，我们也添加了`isSassy()`函数。

当我们使用`Swan`构造函数创建`swan`对象时，这意味着`swan`对象的`__proto__`属性将指向`Swan`的原型。

试一试。可以复制粘贴上面的代码片段，使用`swan.__proto__`查看`swan`的原型。这将返回:

```
> swan.__proto__
{isSassy: ƒ, constructor: ƒ}
    isSassy: () => true
    constructor: ƒ Swan(colour, mood)
    __proto__: Object
```

`swan.__proto__`的`constructor`告诉我们它是使用`Swan`函数构建的，我们还可以看到`isSassy()`函数在原型上`swan.__proto__`如预期链接。

用构造函数创建的对象的一个有用的特性是它允许我们检查它的类型:

```
swan instanceof Swan //returns true
```

[MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof) 将`instanceof`运算符定义如下:

> `**instanceof**` **操作符**测试一个构造函数的`prototype`属性是否出现在一个对象的原型链中。返回值是一个布尔值。

将此应用于我们的示例:

`Swan`构造函数有如下原型:

```
Swan(colour, mood) {
    this.colour = colour;
    this.mood = mood;
}
```

并且`swan.__proto__`具有下面的构造函数属性，就像我们之前演示的那样:

```
Swan(colour, mood) {
    this.colour = colour;
    this.mood = mood;
}
```

因此，当`Swan`原型出现在`swan`的原型链中时，返回布尔值`true`。

需要注意的一件重要事情是，检查了整个原型链。如果你有更复杂的原型链，请记住这一点，同时，在默认情况下，所有新对象`__proto__`属性都指向它们的原型链中的`Object.prototype`，因此检查`<arbitraryObject> instanceof Object`是否总是返回 true。

# JavaScript 中的类并不像你想象的那样

现在我们已经讨论了原型和构造函数的基本原理，在这篇文章中我还想谈一件事。

当我刚从大学毕业，开始专业地使用 JavaScript 时，我花了很多时间在广泛使用的面向对象语言上(对我来说主要是 Java)，看到 JavaScript 中的类并开始将它们联系起来是非常容易的。

在面向对象的语言中，如 Java，类通常作为一个蓝图，并且——排除任何静态行为——每当一个新的对象被实例化时，它被实例化为它自己的副本，并在内存中占据它自己的空间。

然而在 JavaScript 中，类只是语法上的糖，实际上仍然是一个函数。当一个类被“实例化”时，新的对象实际上有一个指向类方法的原型(实际上是原型上的属性！)并且包含引用它的构造函数——听起来熟悉吗？

为了证明我的观点，请看以下内容:

```
class RubberDuckClass {
    constructor(colour) {
        this.colour = colour
    }
    doesFloat() {
        return true;
    }
}function RubberDuckConstructor(colour) {
    this.colour = colour;
}RubberDuckConstructor.prototype.doesFloat = () => true;let rubberDuckClassObject = new RubberDuckClass('yellow');
let RubberDuckConstructorObject = new RubberDuckConstructor('blue');
```

在这个例子中，我们实际上可以证明，一个类实际上只不过是一个穿着花哨衣服的函数

rubberDuckClassObject:

*   将`colour`属性设置为参数(' yellow ')
*   原型:
    -构造函数引用`RubberDuckClass`
    -具有`doesFloat()`功能

rubberDuckConstructorObject:

*   将`colour`属性设置为参数(“蓝色”)
*   原型:
    -构造函数引用`RubberDuckConstructor`
    -具有`doesFloat()`功能

虽然从表面上看，JavaScript 类看起来就像我们在传统的基于类的设计模式中所期望的那样；在本质上，类仍然只不过是具有原型机制提供的继承能力的函数。

在我看来，这就是为什么 JavaScript 中的类会让人误解。最终有一天，作为一名开发人员，你将需要更深入地研究这种语言，到那时，当你发现类可能与你想象的不一样时，你会大吃一惊。