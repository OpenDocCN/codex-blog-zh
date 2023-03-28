# C#介绍技巧—来自 JavaScript，第 2 部分

> 原文：<https://medium.com/codex/introduction-tips-to-c-coming-from-javascript-part-2-6001fd31bf04?source=collection_archive---------11----------------------->

![](img/94477a73d1e6e4e008d7c9a3dfcbb843.png)

沙哈达特·拉赫曼在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

当我试图成长为一名开发人员时，我决定开始学习 C#，因为(在我有限的经验中)我没有看到很多有 it 经验的训练营毕业生，我也看到了一些从事 C#的工作机会。网络堆栈。

如果你不知道的话，C# & the。NET 框架是由微软开发的，他们提供了一整套学习模块。

我现在回到 C#入门系列的第二部分，在这里我介绍了 JavaScript 和 C#之间的一些快速差异，如果你想开始学习 C#，希望能对你有所帮助。

# 字符串插值

在 JavaScript 中，字符串插值是正式编写模板文字，模板文字是由反斜杠 **` `** 分隔的字符串，内嵌的表达式称为替换。这是一种编写字符串和利用存储在变量中的数据的方法，而不需要使用串联方法。

```
const age = 28
const myString = `Hi, I am ${age} years old.`console.log(myString)
// Hi, I am 28 years old.
```

在 C#中，这些模板文字的书写方式略有不同。在 C#中，不使用反斜线来指定该字符串是模板文字，而是在双引号(" ")之外使用美元符号($)。这将字符串划分为模板文字。然后，在字符串中任何你想使用变量的地方，你使用花括号，就像在 JavaScript 中一样。

```
int age = 28
string myString = $"Hi, I am {age} years old."Console.WriteLine(myString)
// Hi, I am 28 years old.
```

# 条件逻辑语句— If / Else

在 JavaScript 中，当您想要合并条件逻辑时，您可以使用关键字、括号内的布尔表达式和花括号来定义代码块。

```
const age = 28
if (age < 30) {
  console.log("You are under 30!")
} else {
  console.log("You are 30+!")
}// You are under 30!
```

在 C#中，这种方式几乎是相同的，尽管使用的间距略有不同。如果你用 C#写了上面的代码——改变变量来声明类型——它仍然可以工作，因为 C#忽略了空格。我下面写的方式更受欢迎，因为它看起来更干净，更容易阅读，尤其是当代码块包含更多的代码行时。

```
int age = 35
if (age < 30)
{
  Console.WriteLine("You are under 30!")
} 
else
{
  Console.WriteLine("You are 30+!")
}// You are 30+!
```

有趣的是，C#有另一种方式，如果你有类似上面代码中的一行程序，你可以编写适用于**的 **if/else** 语句。你不用花括号。再说一遍，就是用一种易读的方式编写代码。如果您尝试以这种方式编写多行代码，它将基于布尔表达式计算第一行代码，但是其余的代码行将运行，而不考虑布尔表达式。**

```
int age = 35
if (age < 30)
  Console.WriteLine("You are under 30!")
else
  Console.WriteLine("You are 30+!")// You are 30+!_ _ _ _ _ _ _int age = 25
if (age > 30)
  Console.WriteLine("You are under 30!")
  Console.WriteLine("Wait, but that statement was false!")
  COnsole.WriteLine("Well, these lines still execute")// Wait, but that statement was false!
// Well, these lines still execute
```

# 声明数组

如果您还记得，在第 1 部分中我提到过，在 C#中，您必须声明您正在处理的变量的数据类型。这也适用于数组。

当你用 JavaScript 创建数组时，有不同的方法。只看创建一个空数组，我们可以用这两种不同的方法:

```
let myArray = []
let myArray2 = new Array()
```

在 C#中，对于数组，必须声明数组内部的数据类型和数组的大小。然后使用索引，您可以着手为数组中的元素赋值。访问和更改数组中元素的值与 JavaScript 相同。如果你已经有想要放入数组的值，你也可以用它们初始化一个数组。

```
string[] myArray = new string[6]string[] myCars = {"Toyota", "Honda", "Subaru"}
```

我希望这篇文章对你开始学习 C#有所帮助。随着我了解的越来越多，我将会有更多的文章强调 C#和 JavaScript 之间的相似和不同之处，以帮助您更轻松地过渡。

编码快乐！