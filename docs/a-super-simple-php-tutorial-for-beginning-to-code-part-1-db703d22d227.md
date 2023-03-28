# 开始编码的超级简单的 PHP 教程—第 1 部分

> 原文：<https://medium.com/codex/a-super-simple-php-tutorial-for-beginning-to-code-part-1-db703d22d227?source=collection_archive---------6----------------------->

![](img/cca94fbb159bcdceb421976595b29054.png)

[KOBU 机构](https://unsplash.com/@kobuagency?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/coding-in-php?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

我以前抱怨过大多数教程写得有多差。但是我能接受我自己的建议吗？我认识一些真正聪明的人，他们从未真正编码过，但发现他们喜欢这样做——我该如何建议他们开始呢？

# 我认为是“好教程”的提纲草稿

*   以一个清晰的教程目的陈述开始——为什么有人想学它？
*   确定绝对最低的先决条件，提供一个关于这些主题的超级简单教程的链接。
*   做一个快速检查，事情都设置好了。
*   列出并清楚地陈述关键*原则*或*概念区别*的简短列表，人们需要理解这些原则才能产生有用的东西，并提供官方手册中包含更多详细信息的页面链接。
*   包括尽可能简单的代码示例——没有依赖性或无关的样式。
*   提供一个“习题集”以便在实践中学习。
*   保持教程开放供评论和改进！所以——请让我知道这个是否会更好。

# 本教程的目的

*   向聪明的初学者提供用 PHP 创建他们的第一个动态 web 应用程序的基本知识(第 1 部分)，如何让页面相互通信[(第 2 部分)](https://johncoonrod.medium.com/a-super-simple-php-tutorial-for-beginning-to-code-part-2-13dbdcc121f2)以及如何让您的页面执行数据库查询(第 3 部分)。
*   专注于一种简单的编码方式。即使在 PHP 中，也有很多方法来做事情。有完全不同的编码范例，你可能听过人们争论。本教程使用“过程的”和“函数的”范例，因为我认为它们更容易学习——不同于 OOP:面向对象的范例。当我们提到数据库时，你会看到一点 OOP。

# 最低先决条件

*   对 HTML 的基本理解。 [**这里的**](https://www.youtube.com/watch?v=PlxWf493en4) 是一个很棒的 30 分钟 YouTube 介绍，这里是我最喜欢的[书面参考](https://www.w3schools.com/html/default.asp)。
*   文本编辑器——最好是能自动检查代码的编辑器。我推荐免费开源的 [Visual Studio 代码](https://code.visualstudio.com/)。
*   PHP 的命令行版本。如果您不清楚是否安装了它，请转到命令行，询问它安装的是什么版本:

```
php -v
PHP 7.4.18 (cli) (built: May  3 2021 11:59:44) ( NTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
    with Zend OPcache v7.4.18, Copyright (c), by Zend Technologies
```

*   如果没有安装，在 windows [上点击这里](https://windows.php.net/download/)。在 chromebook/debian linux 上:

```
sudo apt install php7
```

*   在那个区域创建一个名为 index.php 的文本文件(任何 web 服务器的默认主页，一个包含一行 php 代码的 html 文件。(特殊的标签是 php 嵌入 HTML 的方式。)

```
<?php echo "Hello World! Today is ".date('l'); ?>
```

*   启动 php 内置的本地 web 服务器

```
php -S localhost:8080
```

*   将浏览器指向 localhost:8080，看看它是否显示 Hello World 和星期几。嘣——你的第一个动态网页！要停止本地服务器的运行，只需在命令行中按 ctrl-C。

# 首要原则和定义

*注意:还有很多，但是如果你理解了这些，你就可以开始了。最后我会提到*

*   **代码**是一种特定的**编程语言**中的 **t** ext，它告诉计算机做什么。
*   PHP 是“一种广泛使用的开源通用脚本语言，特别适合 web 开发，可以嵌入到 HTML 中。”
*   [**基本语法**](https://www.php.net/manual/en/language.basic-syntax.php) **:** 要开始，你真的只需要知道语句以分号结尾，大小写重要($hello 不等同于$HELLO)，php 在<中嵌入 html？php …？>标签。在代码中加入注释也很好——也就是说，对人类而不是计算机的解释。php 代码中的注释可以添加在//后面，一行或一个注释块的其余部分可以用/* … */括起来
*   **程序/脚本/应用(“app”)**:这些术语都是描述实际做一些事情的代码集合。它可能存储在一个文件中，也可能存储在数百个文件中。
*   **客户端/服务器:**构建应用程序有三种基本方式:**独立的**应用程序，比如用 C++编写的 Microsoft Word，你可以下载并运行，**服务器端 web 应用程序**比如 Medium 或脸书，远程服务器完成大部分工作并将其发送给你的浏览器(即**“客户端”**)——其中许多是用 PHP 编写的，或者**客户端 web 应用程序**，你的浏览器自己完成工作，几乎都是用 PHP 编写的如今，大多数主流应用都是一个组合，但本教程将只关注服务器端。
*   [**变量**](https://www.php.net/manual/en/language.variables.basics.php) **:** 几乎所有的语言基本上都是代数。x = 3；y = 4；x = x+y；(现在 x 等于 7)。在 PHP 中，所有变量都以$开头，且应该有清楚表达其含义的名称。例如:$ name = " John
*   [**常数**](https://www.php.net/manual/en/language.constants.php) **:** 顾名思义。圆周率是常数。不能写 pi = 7；常量一旦定义，就不能更改。
*   **类型:**变量可以是不同的类型，例如:整数、浮点、文本字符串。在这一点上，我们有整数的例子和一个字符串的例子。在一些语言中，你必须定义一个变量是什么类型——在 PHP 中，它将动态地采用任何显而易见的类型。
*   **操作符:**包括**算术**操作符，如+、-、*(表示乘法)— **逻辑:** and ( & &)，或(||) —字符串串联。例如:

```
$fullname=$first_name." ".$last_name;
```

*   **表达式**是有值的代码位，可能被赋给变量。

```
3+4 
sin(pi)
atan($angle);
substr("Hello World",0,5);
```

*   **语句**是做一件事的代码的单个“句子”。最著名的声明显示在精选图像中:echo“Hello World！”；
*   **块:**作为一个单元对待的一组语句。在 PHP 中，一个块被{}包围，当你处理下一点时，块是至关重要的。
*   **条件和迭代:**程序之所以聪明，是因为代码块可以根据表达式的值有条件地运行或循环。(重要说明！在 PHP 中，你给

```
if ($name=='Bob') {echo "Hi Bob!;} else {echo "Who is $name?";}
for ($i=1;$i<4;$i++) {echo Testing $i;} 
```

*   **数组**是具有一个名称的数值集合，可以通过从 0(不是 1)开始的**索引**来访问，或者作为具有**关键字**的**关联数组**来访问。示例:

```
$fibo=[1,1,2,3,5];
echo $fibo[0]; // this would display "1"
$suits=['Hearts','Diamonds','Clubs','Spaces'];
$capitals=['MO'=>'Jefferson City','IL'=>'Springfield','VT'=>'Montpilier];
```

*   **函数**是一段有名字的代码。你会想把任何有价值的应用程序写成一堆有意义的函数。函数可以返回一个值或者只是做一些事情。例如，PHP 应用程序的主体可能简单地调用一系列函数，然后在之前或之后定义这些函数。

```
// Main program
$name=getUserName();
$school=fetchSchoolName($name);
echo($name." attends ".$school);
sayGoodBye('Folks');// Functions
function getUserName() {
  return 'Sally Ride';
}
// This has a bit of database code in that we'll cover in Part 3
function fetchSchoolName($fullname) {
  $schools=['Sally Ride'=>'Stanford','Joy Reid'=>'Harvard'];
  return $schools[$fullname];
}
// Note - this last one doesn't have a return value.
function sayGoodBye($audience) {
  echo("Goodbye, $audience");
}
```

*   **内置函数:**PHP 中已经定义了数百个函数，特别是用 [**字符串**](https://www.php.net/manual/en/ref.strings.php) 和 [**数组**](https://www.php.net/manual/en/ref.array.php) 做各种事情。您将会大量使用这些手册页！
*   **参数**:示例 fetchSchoolName()传递一个参数——我们想要查找的学校的名称。正如您将在内置函数的手册页中看到的，可以定义**默认值**，这样您就不必填写所有参数。
*   **范围:**这是一个比较棘手的部分，不同的语言处理方式不同。当你定义一个变量时，它存在于代码的什么部分？在 PHP 中，主程序中定义的变量在主程序中是不可访问的，通常在函数中也是不可访问的，反之亦然。有很多方法可以解决这个问题，但是你真的不想尝试。
*   **好做法。编码可能会变得复杂，编码者会告诉自己很多“咒语”。这里有一个他们的[超级短名单](https://betterprogramming.pub/kiss-dry-and-code-principles-every-developer-should-follow-b77d89f51d74)，这里有一个[更长的一个](/swlh/the-must-know-clean-code-principles-1371a14a2e75)(在这一点上他们中的许多人将会是你无法理解的。**

# 第一部分的习题集！

*   创建一个页面，计算并打印 Fibonacci 数列[的前 100 个成员。](https://en.wikipedia.org/wiki/Fibonacci_number)
*   制作一页打印出 Lorem Ipsum 段落，然后按字母顺序排列其中的单词。(提示— [访问手册中的本页](https://www.php.net/manual/en/ref.strings.php)。)

# 您可能想要导出的其他内容

*   **存储库:**通过建立一个 [github](https://github.com) 账户，在云中存储、管理版本和协作。Github 与 VSCode 配合得很好，或者你可以直接从 github.com 网站编辑它。

## [点击此处查看第二部分](https://johncoonrod.medium.com/a-super-simple-php-tutorial-for-beginning-to-code-part-2-13dbdcc121f2)