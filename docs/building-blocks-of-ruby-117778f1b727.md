# 用 Ruby 编程

> 原文：<https://medium.com/codex/building-blocks-of-ruby-117778f1b727?source=collection_archive---------5----------------------->

## [法典](http://medium.com/codex)

Ruby 最常用于构建 web 应用程序。然而，它是一种类似于 Python 的通用语言，因此它有许多其他应用，如数据分析、原型制作和概念验证。

![](img/2c8e1480ff1f56aa32bfc36fe3ad2c56.png)

照片由[阿里萨·查塔萨](https://unsplash.com/@golfarisa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

Ruby 是一种高级的解释型编程语言，由松本幸宏(Yukihiro“Matz ”)在 20 世纪 90 年代中期创建。它是一种通用语言，旨在易于使用和阅读，语法类似于自然语言。Ruby 通常用于 web 开发，以及构建命令行实用程序、桌面应用程序和其他类型的软件。

Ruby 的一个关键特性是它强调程序员的生产力和易用性。Ruby 的语法设计得直观易读，初学者和有经验的程序员都可以使用。Ruby 还包括许多内置特性和库，使得完成常见的编程任务变得容易，比如处理字符串、数组和散列。

Ruby 的另一个重要特性是其面向对象的编程模型。Ruby 中的一切都是对象，可以在对象上定义方法来增加功能。Ruby 还支持继承、封装和多态，这使得它成为构建复杂软件系统的强大工具。

Ruby 还以其广泛的开源 gem 库而闻名，这些开源 gem 是预先构建的代码包，可以很容易地集成到 Ruby 项目中。这些 gem 提供了广泛的功能，从数据库访问到 web 开发框架，并且可以为开发人员节省大量的时间和精力来构建软件。

用 Ruby 构建的最流行的 web 开发框架之一是 Ruby on Rails。Rails 是一个全栈的 web 框架，它提供了一组用于快速、轻松地构建 web 应用程序的约定和工具。由于专注于开发人员的生产力，Rails 已经成为初创公司和小型企业以及大型企业的热门选择。

大多数现代编程语言都有一套类似的构建模块，例如

1.  接收来自用户的输入并向用户显示输出
2.  在变量中存储值的能力(通常是不同种类的，如整数、浮点或字符)
3.  可以存储姓名、地址或任何其他类型文本的字符串
4.  一些高级数据类型，比如可以存储一系列常规变量(比如一系列整数)的数组
5.  循环代码的能力从某种意义上说，你想从一个用户那里接收 10 个名字，你将为此写 10 次代码，但只写一次，并告诉计算机循环 10 次
6.  有条件地执行代码语句的能力，例如，如果分数超过 40，则学生通过，否则失败
7.  将您的代码放在函数中
8.  通过一种或多种基本数据类型(如结构或类)的组合形成的高级数据类型
9.  从磁盘读取文件并将文件保存到磁盘
10.  能够对你的代码进行注释，这样当你过一段时间再次访问它的时候就能理解它

让我们直入主题，看看如何在 Ruby 中做这些事情。

0。如何在桌面上安装 Ruby？

在开始用 ruby 编写程序之前，我们需要设置我们的 Ruby 环境。

1.  你可以从这里安装红宝石【ruby-lang.org。
2.  此外，您需要安装一个 IDE 来编写和执行 Ruby 代码。我个人最喜欢的是 code.visualstudio.com。
3.  最后，您还需要在 VSCode 中安装以下扩展:Ruby(吕鹏)和 Code Runner(韩军)。

现在，让我们编写一个简单的程序，打印 hello world 供用户查看

```
print 'Hello World !!!'
```

## 1.接收来自用户的输入并向用户显示输出

有几种方法可以向用户显示输出。让我们看看显示输出的一些方法:

```
#Method 1:
print 'Hello World !!!'#Method 2:
p 'Hello World !!!'#Method 3:
puts 'Hello World !!!'#Method 4: Showing data stored in variables to user
my_name = "Vivek"
puts "Hello #{my_name}"#Method 5: Showing multiple variables using same puts statement
aString = "I'm a string!" 
aBoolean = true 
aNumber = 42
puts "string: #{aString} \nboolean: #{aBoolean} \nnumber: #{aNumber}"
```

## 2.在变量中存储值的能力(通常是不同种类的，如整数、浮点或字符)

变量有三种主要类型:

1.  字符串(语音标记中的符号集合)
2.  布尔值(真或假)
3.  数字(数值)

以下是一些例子:

```
aString = "I'm a string!"  
aBoolean = true  
aNumber = 42 
puts "string: #{aString} \nboolean: #{aBoolean} \nnumber: #{aNumber}"
```

对数值变量执行基本数学运算。有 6 种基本运算:加、减、乘、除、模和指数。

```
a = 5
b = 2
puts "sum: #{a+b}\
    \ndifference: #{a-b}
    \nmultiplication: #{a*b}
    \ndivision: #{a/b}
    \nmodulo: #{a%b}
    \nexponent: #{a**b}"
```

## 3.可以存储姓名、地址或任何其他类型文本的字符串

字符串可以使用单引号或双引号，任何一种都可以接受。

```
myFirstString = 'I am a string!' #single quotes
mySecondString = "Me too!" #double quotes
```

我们将重点介绍一些常见的操作:

```
"Hi!".length #is 3
"Hi!".reverse #is !iH
"Hi!".upcase #is HI!
"Hi!".downcase #is hi!
# You can also use many methods at once. They are solved from left to right.
"Hi!".downcase.reverse #is !ih
# If you want to check if one string contains another string, you can use .include?.
"Happy Birthday!".include?("Happy")
```

## 4.一些高级数据类型，比如可以存储一系列常规变量(比如一系列整数)的数组

数组允许您将多个值组合在一个列表中。数组中的每个值称为一个“元素”。a .定义数组:

```
myArray = []  # an empty array
myOtherArray = [1, 2, 3]  # an array with three elements
```

b.访问数组元素:

```
# In order to add to or change elements in an array, you can refer to an element by number.
myOtherArray[3] = 4
```

## Ruby 有另一种高级数据类型，叫做 Hash，类似于 python 字典

就像数组一样，哈希允许您将多个值存储在一起。然而，数组使用数字索引存储值，而哈希使用键-值对存储信息。散列中的每条信息都有一个唯一的标签，您可以使用该标签来访问该值。a .若要创建哈希，请使用 Hash.new 或 myHash={}。例如:

```
myHash=Hash.new()
myHash["Key"]="value"
myHash["Key2"]="value2"
# or
myHash={
    "Key" => "value",
    "Key2" => "value2"
}
```

b.要访问散列的元素:

```
puts myHash["Key"] # puts value
```

除了使用字符串作为键，还可以使用符号，如下所示:a .要创建哈希，请使用 Hash.new 或 myHash={}。例如:

```
myHash=Hash.new()
myHash[:Key]="value"
myHash[:Key2]="value2"
# or
myHash={
    Key: "value",
    Key2: "value2",
}
```

b.要访问散列的元素:

```
puts myHash[:Key] # puts "value"
```

## 5.循环代码的能力从某种意义上说，你想从一个用户那里接收 10 个名字，你将为此写 10 次代码，但只写一次，并告诉计算机循环 10 次

Ruby 有几个循环选项(For、While 和 Until)。有嵌套选项(单个、两个、三个，..)循环也是如此。For 循环对表达式中的每个元素执行一次代码。以下示例显示了 for 循环的工作原理:

```
# Syntax
for variable [, variable ...] in expression [do]
   code
end# Example
for i in 0..5
    puts "Value of local variable is #{i}"
end
```

b.当条件为真时，While 循环执行代码。while 循环的条件与代码由保留字 do、换行符、反斜杠或分号分隔。。以下示例显示了 for 循环的工作原理:

```
# Syntax
while conditional [do]
    code
end# Example 
a=1
b=5
while a<=b
    puts "run #{a}" 
    a=a+1 
end# Ruby while modifier - Executes code while conditional is true.
code while condition
# or
begin # If a while modifier follows a begin statement with no rescue or ensure clauses, code is executed once before conditional is evaluated.
    code 
end while conditional
```

c.当 conditional 为 false 时，Until 循环执行代码。until 语句的条件语句通过保留字 do、换行符或分号与代码分隔开。以下示例显示了 for 循环的工作原理:

```
# Syntax
until conditional [do]
    code
end # Example
$i = 0
$num = 5
until $i > $num  do
   puts("Inside the loop i = #$i" )
   $i +=1;
end# Ruby until modifier - Executes code while conditional is false.
code until conditional
# or 
begin # If an until modifier follows a begin statement with no rescue or ensure clauses, code is executed once before conditional is evaluated.
    code
end until conditional
```

d.Ruby 还提供了以下关键字，可以修改上述循环的行为:

```
# break - Terminates the most internal loop. Terminates a method with an associated block if called within the block (with the method returning nil).# next - Jumps to the next iteration of the most internal loop. Terminates execution of a block if called within a block (with yield or call returning nil).# redo - Restarts this iteration of the most internal loop, without checking loop condition. Restarts yield or call if called within a block.# retry - If retry appears in rescue clause of begin expression, restart from the beginning of the begin body.# retry - If retry appears in the iterator, the block, or the body of the for expression, restarts the invocation of the iterator call. Arguments to the iterator is re-evaluated.
```

## 6.有条件地执行代码语句的能力，例如，如果分数超过 40，则学生通过，否则失败

条件用来给你的程序增加分支逻辑；它们允许你包含只在特定条件下发生的复杂行为。a . If-If 条件是一个可以检查其真实性的表达式。如果表达式的计算结果为 true，则执行块中的代码。

```
if condition
    something to be done
end# Ruby if modifier - executes code if the conditional is true.
code if condition
```

下面是一个带有 elsif 和 else 的 if 语句的实际示例。

```
booleanOne = true
randomCode = "Hi!"
if booleanOne
  puts "I will be printed!"
elsif randomCode.length>=1
  puts "Even though the above code is true, I won't be executed because the earlier if statement was true!"
else
  puts "I won't be printed because the if statement was executed!"
end
```

b.If Else 您可以将 If 与关键字 Else 结合使用。这允许您在条件为真时执行一个代码块，在条件为假时执行另一个代码块。else 块只有在 if 块不运行时才会被执行，所以它们永远不会同时被执行。

```
if condition
    something to be done
else
    something to be done if the condition evaluates to false
end
```

c.Elseif —当您想要两个以上的选项时，可以使用 elsif。这允许您添加更多要检查的条件。仍然只有一个代码块将被运行，因为该语句只执行第一个适用块中的代码；一旦条件得到满足，整个语句结束。以下是 if/elsif/else 语句语法:

```
if condition
  something to be done
elsif different condition
  something else to be done
else
  another different thing to be done
end
```

d.除非—如果 conditional 为 false，则执行代码。如果条件为真，则执行 else 子句中指定的代码。

```
unless condition
    # thing to be done if the condition is false
else
    # else is optional
    # thing to be done if the condition is true
end# Ruby unless modifier - Executes code if conditional is false.
code unless conditional
```

e.Case 这基本上与 if-elseif-else 语句相同，但语法更清晰。

```
# case statement syntax
case expr0
when expr1, expr2
   stmt1
when expr3, expr4
   stmt2
else
   stmt3
end
# is basically similar to the following −
if expr1 === expr0 || expr2 === expr0
   stmt1
elsif expr3 === expr0 || expr4 === expr0
   stmt2
else
   stmt3
end
```

案例陈述示例

```
$age =  5
case $age
when 0 .. 2
   puts "i will not be printed"
when 3 .. 6
   puts "i will be printed"
when 7 .. 12
   puts "i will not be printed"
when 13 .. 18
   puts "youth"
else
   puts "i will not be printed"
end
```

## 7.将您的代码放在函数中

a.在 Ruby 中，我们称函数为方法。方法是在程序中执行特定任务的可重用代码部分。使用方法意味着我们可以编写更简单、更易读的代码。

```
# syntax
def methodname
    # method code here
end
```

b.还可以定义方法来接受和处理传递给它们的任何参数:

```
# Methods With Parameters
def laugh(number)
    puts "haha " * number
end
```

c.我们可以使用方法名调用方法，并在 paranthesis 中指定参数，也可以不指定参数:

```
# Using method - calling method as follows prints "haha" 5 times on the screen
laugh(5)
# You can also call laugh without paranthesis
laugh 5
```

d.我们可以为参数设置默认值，如果在没有传递所需参数的情况下调用方法，将会使用这些默认值

```
def method_name (var1 = value1, var2 = value2)
    expr..
end
```

e.我们也可以返回值。ruby 中的 return 语句用于从 Ruby 方法返回一个或多个值。

```
return
# or
return 12
# or
return 1,2,3
```

f.我们还可以定义参数数量可变的方法，如下所示:

```
Variable Number of Parameters
def sample (*test)
    puts "The number of parameters is #{test.length}"
    for i in 0...test.length
       puts "The parameters are #{test[i]}"
    end
end
sample "Zara", "6", "F"
sample "Mac", "36", "M", "MCA"
```

## 8.通过一种或多种基本数据类型(如结构或类)的组合形成的高级数据类型

Ruby 允许用户创建类。这些可以是变量和对这些变量进行操作的函数的组合。让我们看看如何定义和使用它们。

```
# Define a class
class employee
    @@no_of_customers = 0
    def initialize(id, name, addr)
       @cust_id = id
       @cust_name = name
       @cust_addr = addr
    end
end# Creating an object of the class and using that
cust1 = employee.new("1", "Vivek", "Somewhere on the, Internet")
```

## 9.从磁盘读取文件并将文件保存到磁盘

让我们看看如何有组织地读取和解析 csv。CSV 是数据科学中最常用的文件类型，但是 ruby 也可以读取其他几种文件类型。

```
require 'csv'# read a csv 
CSV.read("file.csv")# parse a string of text which is in csv format
CSV.parse("1,penny\n2,nickel\n3,dime")
```

## 10.能够对你的代码进行注释，这样当你过一段时间再次访问它的时候就能理解它

a.我们可以告诉 ruby，一行代码以#开头就是一个注释。

```
#this is a comment
```

b.我们还可以指定一个注释块，如下所示:

```
=begin
There are three main types of variable:
1\. Strings (a collection of symbols inside speech marks)
2\. Booleans (true or false)
3\. Numbers (numeric values)
=end
```

总的来说，Ruby 是一种强大而灵活的编程语言，非常适合各种各样的编程任务。由于注重易用性、面向对象的设计和丰富的宝石库，Ruby 对于初学者和有经验的程序员都是一个有价值的工具。无论是构建 web 应用程序、桌面实用程序还是其他类型的软件，Ruby 都提供了快速、灵活和愉快的开发体验。

最后，我将强调在学习任何新东西时练习的重要性。坚持不懈，尝试这些积木的不同组合，先解决简单的问题，再解决复杂的问题，是变得流利的唯一途径。

欢迎评论！