# C#初学者备忘单

> 原文：<https://medium.com/codex/c-cheat-sheet-for-beginners-4536358187d?source=collection_archive---------6----------------------->

![](img/993291c5e5725e5bd3edf1665e2ab427.png)

照片由[戴恩·托普金](https://unsplash.com/@dtopkin1?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 程序结构

```
using System;

namespace HelloWorld
{
  class Program
  {
    static void Main(string[] args)
    {
      Console.WriteLine("Hello World!");    
    }
  }
} 
```

# 控制台输出

要在控制台中打印/显示文本，请使用以下语句。

```
Console.WriteLine("Hello World!");
```

“Hello World”是将在用户控制台中打印的文本。

# 控制台输入

要读取用户的输入，请使用以下语句。

```
String x = Conso­le.R­ea­dLi­ne();
```

上面的代码读取用户的输入，并将其保存在变量 **x** 中。

# 变量和数据类型

变量是内存中存储一些信息空间。

要创建一个变量，您应该使用下面的代码

```
*DataType variableName = value;*
```

## 声明示例

```
// create a variable with name x to save a integer number
int x;// create a variable with name age to save a integer number
// and give it value 18
int age = 18;// create a variable with name age to save a string (text data type)
string name = "John";
```

## 赋值

在变量被创建之后，我们可以在任何时候指定或改变它的值。

```
x = 1000;age = 25; // change the value of variable to 25name = “John Snow”;
```

## 数据类型

C#支持许多数据类型，但最初您可以专注于这些类型。

**int** —存储整数

```
int weight = 56;
```

**字符串** —存储文本，文本应该总是在" "(引号)内。

```
string address = “Cape Vert, Assomada”
```

**double** —存储一个十进制数(将整数与我们使用的小数位分开。)

```
double note = 9.5;
```

**bool** —存储一个布尔值，假设为真或假

```
bool approved = true;
```

**char** —存储单个字符，应该总是在' '

```
char letter = ‘A’;
```

# 铸造/类型转换

造型或类型转换是将一种数据类型转换为另一种数据类型的过程。

要将字符串转换为整数，可以执行下列代码之一。

```
// example 1
int num = int.Parse("3");// example 2
int num2 = Convert.ToInt32("5");
```

要将一个十进制数转换成整数，你可以像下面的代码那样做。

```
// example 1
double x = 9.5;
int num = (int) x;// example 2
double x2 = 9.5;
int num2 = Convert.ToInt32(x2);
```

# 经营者

## 算术

这种类型的运算符用于执行数学运算。

**瘾** (+)，+运算符用来对两个值求和。

```
int a = 7;
int b = 3;
int c = a + b;
```

**减法**(-),-运算符用于将一个值减去另一个值。

```
int d = a - b;
```

**乘法** (*)，*运算符用于两个值相乘。

```
int e = a * b;
```

**除法** (/)，/运算符用于将一个值除以另一个值。

```
int f = a / b;
```

**模数** (%)，%运算符用于得到除法余数。

```
int g = a % b;
```

**增量** (++)，++运算符用于将变量值增加 1。

```
int h= 2;
h++;
// the h now will take the value 3
```

**减量**(-),-运算符用于将变量值减 1。

```
int i = 10;
i--;
// the i now will have the value 9
```

## 属性

这种类型的运算符用于给变量赋值。

**赋值** (=)，运算符=用于给变量赋值。

```
int a = 15;
```

**加法赋值** (+=)，运算符+=用于给变量加值。

```
int b = 8;
b += 8;
// the code above is the same thing as
// b = b + 8;
```

**减法赋值** (-=)，运算符-=用于减去变量中的一个值。

**乘法赋值** (*=)，运算符*=用于将一个值乘以一个变量。

**除法赋值** (/=)，运算符/=用于对变量中的一个值进行除法运算。

## 比较

比较运算符用于比较两个值。

**等于** (==)，运算符==用于验证两个变量是否有相同的值。

```
int x = 3;
int y = 4;
bool isEqual = x == y;// the isEqual variable will take the value false because those two variables dont have the same value
```

**不一样**(!=)，接线员！=用于验证两个变量是否有不同的值。

**大于** ( >)，运算符>用于验证一个变量的值是否大于另一个变量的值。

**大于或等于** ( > =)，运算符> =用于验证一个变量的值是否大于或等于另一个变量的值。

**小于** ( <)，运算符<用于验证一个变量的值是否小于另一个变量。

**小于或等于** ( < =)，运算符< =用于验证一个变量是否小于或等于另一个变量的值。

# 情况

## 如果

**if** 用于指定某个值为真时将执行的代码块。

```
int age = 20;if (age > 18) 
{
    Console.WriteLine("The age is greater than 18");
}
```

## …其他

**else** 语句用于指定一个代码块，如果某些为假，将执行该代码块。

```
if (age > 18) 
{
    Console.WriteLine("The age is greater than 18");
}
else
{
    Console.WriteLine("UPS!!! The age is not greater than 18");
}
```

## …否则如果…

**else if** 语句用于指定要验证的新条件是 before 条件为假。

```
if (age > 18) 
{
    Console.WriteLine("The age is greater than 18");
}
else if (age > 14) 
{
    Console.WriteLine("The age is greater than 14");
}
else
{
    Console.WriteLine("UPS!!! The age is not greater than 14");
}
```

## 开关盒

如果某个变量值匹配，使用 **switch** 语句来选择要执行的代码块。

```
int day = 4;switch (day) 
{
  case 1:
        Console.WriteLine("Monday");
    break; case 2:
        Console.WriteLine("Tuesday");
    break; case 3:
        Console.WriteLine("Wednesday");
    break; case 4:
        Console.WriteLine("Thursday");
    break; case 5:
        Console.WriteLine("Friday");
    break; case 6:
        Console.WriteLine("Saturday");
    break; case 7:
        Console.WriteLine("Sunday");
    break;
}
```

# 环

有了循环，只要达到某个条件，我们就可以执行一段代码。

## 在…期间

只要一个条件为真， **while** 循环就会重复执行一些代码块。

```
int i = 0;
while (i < 5) 
{
    Console.WriteLine(i);
    i++;
}
```

## 做/当

**do/while** 循环是 while 循环的变体，主要区别在于在 **do/while** 中，代码块将至少执行一次，并且只要条件为真，就会重复执行。

```
int i = 0;
do 
{
    Console.WriteLine(i);
    i++;
}
while (i < 5);
```

## 为

当您知道循环将重复的次数时，使用**循环。**

```
for (int i = 0; i < 5; i++) 
{
    Console.WriteLine(i);
}
```

## 中断/继续

你可以使用 **break** 语句跳出循环。

```
for (int i = 0; i < 10; i++) 
{
    if (i == 4) 
    {
        break;
    }
    Console.WriteLine(i);
}
```

你可以使用**继续**语句跳转到下一个循环迭代，

```
for (int i = 0; i < 10; i++) 
{
  if (i == 4) 
  {
    continue;
  }
  Console.WriteLine(i);
}
```

# 功能/方法

方法是一个代码块，它只在被调用时运行。

它可以接收数据(参数)并返回一些值。

您可以创建 on 函数，执行以下代码

```
ReturnDatatype functionName(Parameter1Datatype parameter1Name)
{
    // some code
     return;
}
```

下面是一个创建两个数相加函数的例子。

```
// function declaration
int sum(int x, int y)
{
    return x + y;
}// call the function
int c = sum(4, 5);// it will return the value of the sum 4 + 5
```

# 排列

数组是一种可以存储多个值的变量。

要声明一个数组，我们应该按照下面的代码做

```
DataType[] variableName;
```

## 例子

```
string[] students;// or declare and assign values
int[] age = {18, 25, 30};
```

要访问或赋值我们应该引用哪个位置，这个位置总是从零开始引用，一直到 n -1，这里 n 是数组的大小。

## 例子

```
string[] students = { “John”, “Katty”, “Robert” };// access value in the first position
Console.WriteLine(students[0]);// change value in the second position
students[1] = "Kathy";
```

# 班级

类是创建许多具有相同属性的对象的一种方法。

汽车是阶级，沃尔沃是阶级汽车的对象。

人是一个类，约翰是一个类对象。

**查看如何创建一个类。**

```
class Person
{
    string name;
    int age;
}
```

**创建一个我们能做的类的对象。**

```
Person person1 = new Person();
person1.name = "John";
person1.age = 25;
```

# 参考

[](https://www.w3schools.com/cs/index.php) [## C#教程

### C# (C-Sharp)是一种由微软开发的程序设计语言。NET 框架。C#是用来开发…

www.w3schools.com](https://www.w3schools.com/cs/index.php) 

# 让我们鼓掌:)