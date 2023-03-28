# 这就是 Python 被讨厌的原因

> 原文：<https://medium.com/codex/heres-why-python-is-hated-784cbf11c9a5?source=collection_archive---------0----------------------->

## 不受欢迎的信仰

![](img/25fa0f9ac047e90a84b16f41025787de.png)

[大卫·克洛德](https://unsplash.com/@davidclode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你惊讶吗？我没有。我在这儿等你。你是带着两种想法中的一种来阅读这篇文章的:

1.  每个人都喜欢 Python，为什么这篇文章声称它是讨厌的？
2.  哦是的！终于有人认同我了。

# 缩进很好，但是拜托！

我们都使用过 Python，在某个时候，我们已经看到了臭名昭著的`IndentationError`。在某些网站上，你根本无法用 Python 编码，因为无论你做什么，它总是会导致一个`IndentationError`如 Repl.it。你会得到一个额外/缺少空格的错误。当您创建大型项目时，保持缩进也是有问题的。

# 任何事情都可能是变量

当我第一次听到这个的时候，我真的很惊讶。默认情况下，任何东西都可以是变量。你可以用希腊语、俄语或者其他奇怪的语言编写你的 python 程序！

```
αβγδε = 59
print(αβγδε)__ = [2, 3, 4]
for Я in __:
  print(Я)
```

相信我——这行得通。

# Python 是无序的

就在一个小时前，我正在和一个朋友聊天，抱怨 Python 的缺点。语言中的 OOP 概念是我不喜欢的。

## 没有什么是真正的隐私

你可以在类中声明一个变量，但是你不能把它标记为私有。这违反了封装法，在封装法中，您需要捆绑数据，并允许对数据组件进行有限的访问。不管你在变量前加多少下划线，你仍然可以访问和修改数据。

## 在构造函数外初始化变量

在其他语言中，你有一个初始化变量的构造函数(和一个析构函数并清理内存)。在使用对象之前设置对象的需求是有意义的。但是在 python 中，我可以在任何地方定义一个属性，并在任何地方访问它。

```
class Person
  def __init__(self, name):
    self.name = name def walk_for_10_miles(self):
    self.distance_walked = 10 def isHappy(self):
    if len(self.name) > 7:
      return True
```

这段代码让我想到了我的下一个观点——无组织

## 函数中没有参数或返回类型

看 Python 中的函数，你永远不知道函数接受什么，它会返回什么。在前面的代码中，要么`isHappy`将返回`True`，要么什么都不返回。也许它可以返回另一个对象的实例，或者一个字符串，或者一个数字。

## 没有主函数

是的——有些人实际上认为这是一件好事，但它增加了混乱。可能有 20 个文件，但我从哪一个开始呢？我可以拥有`if __name__ == '__main__':`条件，但它并不存在于每个文件中？那么我应该从哪里开始运行代码呢？

# 它通常是第一种也是最后一种编程语言

我对这个标题有两种不同的理解:

1.  你从 Python 开始，不喜欢，就放弃了。
2.  你喜欢编程，喜欢 Python，但是你已经习惯了，不想再学习另一种语言。

第一个是可以理解的——编程并不适合每个人，人们会放弃。但第二个问题与我有关，因为我已经看到了它的发生。

学 Python 的人没有定义变量类型、函数返回类型或者使用花括号的习惯。此外，还有访问说明符和函数重载的严格概念。在 Python 成为第一种语言之后学习另一种语言包括大量的工作，仅仅是习惯这种新语言。他们最终坚持使用 Python。

# 结论

每个人都有权发表自己的意见。然而，在过去的一周里，我遇到了一些人表达了他们对 Python 的厌恶。因为我和他们有着相同的观点，所以我决定阐明 Python 相对于其他语言的确切缺点。Python 是一种非常简单的语言，很容易学习——也许太容易了，因为大多数语言目前有许多 Python 程序员不知道的概念。

我希望你喜欢读我的文章。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```