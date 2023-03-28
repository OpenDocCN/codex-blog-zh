# C#技巧和特性

> 原文：<https://medium.com/codex/c-tricks-features-9940b5e7b4ed?source=collection_archive---------1----------------------->

![](img/3e7d60479b2aae0272e0170aacc90fb5.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

## 任何编程语言都有其技巧和用途。

理解任何编程语言背后的技巧都可以使你的代码更短更高效。

# 类中的表达式体成员

在 C# OOP 中，我们可以找到一些有用的工具，它们可以在编写代码时节省我们大量的时间和空间。

```
public class SampleClass
{
    public int x { get; set; }
    public int y { get; set; }
    public int z => x + y; //automaticlly holds the sum
}
```

在下面的例子中，我们可以看到两个基本的变量声明。

x & y 变量的声明意味着我们可以从任何地方获取和设置它们的值。

然而，第三个声明意味着变量 z 将总是存储 x 和 y 的和。这意味着每次我们改变变量 x 或 y，z 存储的值将自动更新为当前 x 和 y 的和。

通过使用这个表达式体成员表达式，我们不需要在每次改变这些值时都使用函数或操作。

**注意**这个操作符也可以等同于返回值的函数。

```
public class SampleClass
{
   public int x { get; set;}
   public int y { get; set;}
   public double z => Avrage(x, y);//automaticlly holds the average public double Average(int x, int y)
   {
     double sum = x + y;
     return sum / 2.0;
   }
}
```

在上面的代码中，z 的值总是 x & y 的平均值。

这是另一个例子:

```
int x = 5;
int y = 6;
int z => x + y; //stores 11 on the beginningpublic void IncX()
{ 
 this.x++; /* now x = 6 and z = 12 because we increased x by one and now z is updated to the current sum of x & y which is 12*/
}
```

# 特殊关键字

## 的名称

```
int myVariable = 5;
Console.WriteLine(nameof(myVariable)); /*prints the name of the variable*/
```

下面几行代码使用了一个名为 nameof 的特殊关键字。这个关键字基本上返回您传递到的变量的名称。

在本例中，代码将打印“my variable”；

## 转到

在我们开始讨论 goto 关键字之前，您应该知道，尽管 goto 关键字看起来非常独特和有用，但理解使用它是**而不是**一个好的实践，但它是一个很好的工具，这一点很重要。

如果你是一个汇编爱好者，那么你很可能已经熟悉 goto 关键字。goto 关键字就像汇编中的“jmp”关键字一样。

```
int number = 45;
goto nextLabel;
Console.WriteLine("My Number is 5"); //not be executed
nextLabel:
  Console.WriteLine(number); //will be executed
```

goto 关键字的主要思想是跳过代码行，跳到一个标签中。

例如，在这几行代码中，打印“My Number of 5”的那一行将永远不会被执行，因为 goto 关键字跳过了它并跳转到了“nextLabel”。

所以屏幕上唯一会显示的是数字的值。

```
int number = 5;
goto myLabel;
if(number == 7)
{
myLabel:
  Console.WriteLine("I like the goto keyword!");
}
```

例如，如果您知道 C 中的 goto 关键字，那么您可能会认为下面的代码是合法的，但是，在 C#中，goto 关键字是不同的，因为这些代码是无效的。毕竟，goto 关键字不能用于跳转到条件的执行块，就像我们在这个例子中看到的那样。

## 参考和输出

在 C#中，我们可以使用 ref & out 关键字来更改作为参数传递给函数的变量的值，并在函数外部更新它们的值。

为此，C#提供了两个不同的关键字，但是，它们之间有什么区别呢？嗯， **out** 关键字接受参数，但不接受它的值，我们不能使用这个值，我们必须在函数内部初始化它，因为这样，我们可以传递给函数一个变量，这个变量没有初始化为参数。

```
int b; //not initialized
Initialize(out b); //valid 
```

关键字 **ref** 也改变函数外部的参数值(如果你做了改变)，但是你不必初始化函数内部的值，你也不能接受未初始化的变量作为参数。

```
int b; //not initialized
Initialize(ref b); //not valid
```

当我们声明一个参数为 **ref** 或 **out** 时，我们必须写入参数 out/ref，然后是类型，然后是变量名，例如(out int b)。

当我们调用函数时，我们应该写出/ref 和变量名(无类型声明)。下面是一个示例，说明当我们使用这些关键字时，应该如何在函数内和函数外声明:

```
public class Program
{
  public static void Main(string[] args)
  {
     int a = 5;
     int b = 6;
     Increase(out b, ref a); //now b = 0, a = 6
     Console.WriteLine(a + " " + b); //prints 6 0
  }public static void Increase(out int b, ref int a)
  { 
     a++; // increase the value by 1
     b = 0; //requires initializing the value
  }
}
```

**注意**-如果您没有初始化函数内 **out** 参数的值，将会出现错误。

# 地区

我们使用区域来简单地声明一段代码的用途。区域很像注释，但是它们的声明和用法是不同的。

```
#region sum
int x = 5;
int y = 6;
Console.WriteLine(x + y);
#endregion
```

这里的区域显示，位于该区域内的任何东西都代表总和。当我们编写代码并希望展示代码块的作用时，使用这种编码方式会很有用。

**注意**Sum 中的所有代码都会被执行，而不是像注释中那样。

# 可空 <t>& T？</t>

在 C#中，我们可以将任何值类型设置为可空类型。
**注意**引用类型已经可以为空，所以我们不需要为它们做任何事情(ReferenceType？不会引发错误，但可空的< ReferenceType >会)。

可空类型意味着我们可以让任何类型的变量在他可以存储的其他值中存储 null。

比如，我们可以让一个整数存储 null，除了他已经可以存储的整数。

```
int? num = null; //legal
num = 5; //legal
```

要声明一个变量可以为空，我们有两种不同的方法。

```
Nullable<int> integer = null; //Example 1
int? integer2 = null // Example 2
```

恭喜你！你现在知道了一些 C#可以提供的很酷的工具。

希望你喜欢:)