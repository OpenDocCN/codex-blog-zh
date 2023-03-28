# C#介绍技巧—来自 JavaScript，第 1 部分

> 原文：<https://medium.com/codex/introduction-tips-to-c-coming-from-javascript-part-1-8d4709ec1308?source=collection_archive---------14----------------------->

![](img/c25e938ee1361157e50fa10159bdc4c3.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在熨斗学校期间，我学习了 Ruby 和 JavaScript 语言。毕业后，我继续练习这两种语言，但主要集中在 JavaScript 上，尤其是在练习我的编码算法时。我决定开始学习 C#，因为(在我有限的经验中)我没有看到很多有 it 经验的训练营毕业生，我也看到了一些从事 C#的工作机会。网络堆栈。

如果你不知道的话，C# & the。NET 框架是由微软开发的，他们提供了一整套学习模块。此外，他们提供认证考试，这是一个很好的奖励。

今天，我只想回顾一下我所看到的 JavaScript 和 C#之间的一些快速差异，如果你想开始学习 C#，希望能对你有所帮助。

# 分号

在 JavaScript 中，当你写一行代码时，通常应该以分号结束。然而，如果你懂 JavaScript，它会让某些事情顺其自然，你可以不使用分号。

另一方面，C#要求每条语句的末尾都有一个分号。语句可以是多行，但它总是需要一个分号。如果不这样做，它将抛出一个错误。

```
error CS1002: ; expected
```

如果你使用 VisualStudio 代码编辑器(也是微软的产品)，他们有有用的语法下划线来确保你不会错过这个。

# 将数据输出到控制台

在 JavaScript 中，当您想要向控制台输出某些内容时，您可以这样做:

```
console.log("Hello World")
console.log(3 + 3)// Hello World
// 6
```

在 C#中，这种方式非常相似，只是语法略有不同。**控制台**是 C#中的一个类，输出到控制台的方法是 **WriteLine()。**

```
Console.WriteLine("Hello World");
Console.WriteLine("3 + 3");// Hello Word
// 6
```

有趣的是，C#还有另外一个输出到控制台的方法叫做 **Write()** 。两者的区别在于 **WriteLine()** 打印数据时会添加一个新行。因此，如果我们使用 Write()来代替上面的例子，它看起来会像这样。请注意，输出之间也没有空格。

```
Console.Write("Hello World");
Console.Write("3 + 3");// Hello Word6
```

# 声明变量的数据类型

当你在 JavaScript 中声明一个变量时，你有 3 个不同的关键字: **var，let，**和 **const** 。通常， **var** 已经很少被使用了，因为它不同的作用域角色和被提升的能力导致了很多错误。这篇 [StackOverflow 文章](https://stackoverflow.com/questions/762011/whats-the-difference-between-using-let-and-var)很好地解释了其中的区别。不管怎样，在 JavaScript 中，你使用这些关键字来声明一个变量，这个变量可以包含任何数据类型。

```
let myString = "Hello World"
const myAge = 28
```

在 C#中，变量的工作方式不同，因为要创建变量，必须从一开始就声明变量的数据类型。C#中一些常见的数据类型有 **string、int、bool、double** 和 **char** 。C#也是区分大小写的，所以命名约定就像 JavaScript 中使用 camelCase 一样。在声明变量之后，你不需要再写类型了。

```
string myString = "Hello World";
int myAge = 28;Console.WriteLine(myString)
// Hello World
```

我希望这篇文章对你开始学习 C#有所帮助。随着我了解的越来越多，我将会有更多的文章强调 C#和 JavaScript 之间的相似和不同之处，以帮助您更轻松地过渡。

编码快乐！