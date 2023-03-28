# 我们可以订购什么？

> 原文：<https://medium.com/codex/what-can-we-moq-d2d5caa0c8bf?source=collection_archive---------8----------------------->

## 最受欢迎和友好的嘲笑图书馆。网

![](img/2b08e96c0a23cae88c1814ce6d4d2cda.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=medium&utm_medium=referral) 拍摄的照片

很多时候，当我们想在单元测试中测试一个组件时，这个组件会使用作为其依赖的对象。模拟对象用于测试，作为真实实现的替代。我们可以为模拟对象配置不同的行为，这样我们就可以测试组件来正确处理所有的行为。

# 我们能嘲笑什么？

我们可以将 mock 视为动态创建的类型的实例，它实现了接口。模拟对象也可以是从类继承的实例，并且能够重写该类的虚方法，并实现其抽象方法。

这就是为什么我们不能测试依赖于私有或静态方法的情况(至少没有简单的方法可以做到)。在静态方法的情况下，我们可以像这里的[建议的那样使用包装器来解决。](/@martinrybak/how-to-mock-singletons-and-static-methods-in-unit-tests-cbe915933c7d)

**嘲讽*接口*方法和*属性* :**

在这个例子中，我们有一个接口 *IEmailValidator* ，它有一个包含字符串列表的属性和一个验证电子邮件地址的方法。 *UserController* 在更新用户邮箱时使用这个接口。

当我们想要测试控制器的 *UpdateEmail* 方法时，我们需要模拟 *IEmailValidator* 。 *SetUpGet* 是配置该接口返回属性值的方法(第 8 行)，在第 9 行可以看到调用 *Validate* 方法时如何配置返回值:

**嘲讽*抽象*和*虚拟*方法:**

这里有一个例子，我们可以模拟一个从抽象类继承的对象，这个抽象类既有一个虚方法，又有一个抽象方法。抽象方法是需要由子类实现的方法。虚方法是一种由抽象类实现的方法，但是子类总是可以覆盖这个实现。

在这个例子中，子类( *TextFileReader* )没有覆盖抽象类实现。当模仿 *TextFileReader，*时，您可以看到有两个如何配置虚拟方法的选项:

1.  设置一个新的返回值，如第 2 行所述。
2.  使用 CallBase()，它将 mock 配置为使用父类已经为该类实现的行为(第 6 行)。这对每一个虚方法都是正确的，不管它是在抽象类中实现还是在常规类中实现。

抽象方法的配置和我们在接口中的配置是一样的。

**嘲讽*保护*方法:**

在这个例子中，我们有一个使用了 *HttpClient* 的类。当我们想要测试 *GetCarPrice* 方法时，我们需要找到一种模仿 *HttpClient -* 的方法，因为这个方法依赖于 *HttpClient。*

不幸的是，我们不能模仿它的 *GetAsync* 方法，因为 *HttpClient* 不是一个接口，这个方法既不是抽象的也不是虚拟的。

***起订量。受保护的*** 名称空间给了我们模仿受保护方法的能力。 *Protected()* 只是给你访问权(大概是通过反射，因为它是基于字符串的)来覆盖受保护的成员。

*HttpClient* 依赖于 *HttpMessageHandler。*无论何时调用 *GetAsync* 方法，它都会通过 *HttpMessageHandler 的 SendAsync* 方法。这是一个受保护的方法，因此我们可以模拟它。我们可以将模拟的 *HttpMessageHandler* 注入到 HttpClient 中，并由此模拟 *GetAsync* 方法*的返回值。*

> 所以继续用 Moq 嘲讽吧😃