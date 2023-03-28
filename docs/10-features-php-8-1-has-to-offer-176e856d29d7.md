# PHP 8.1 必须提供的 10 个特性

> 原文：<https://medium.com/codex/10-features-php-8-1-has-to-offer-176e856d29d7?source=collection_archive---------2----------------------->

![](img/b4ff5f01ef01cdc3c220d7860b3869d5.png)

PHP 的新版本(8.1)将于 11 月 25 日发布。它可以提供许多新功能，越来越接近更现代的语言。

这里列出了 PHP 8.1 中添加的 10 大新特性。

# 1.交叉点类型

PHP 8.1 将支持交集类型，这与并集类型相反(允许任何声明的类型)。交集类型允许声明属性、参数或返回类型，并强制该值属于所有声明的类/接口类型。

```
function drink_and_wash(***Drinkable&Washable*** $drink) {
    ...
}
```

在上面的代码中， **$drink** 必须是一个同时实现了**可饮用**和**可清洗**的类中的对象——否则，将会引发类型错误。

## 仅类和接口

交集成员只能是类和接口，所以不允许标量类型、数组、void、callable、never、iterable、null、mixed 等。

```
function foo(***string*|*int*** $val) {}*// Type Error* *Fatal error: Type string cannot be part of an intersection type in ... on line ...*
```

# 2.从不—一种新的返回类型

PHP 8.1 中出现了一种新的返回类型——*never*。当声明一个返回类型为 **never** 的函数时，你基本上可以说它将**永不返回值**并且**返回一个异常**或者**用一个 die/exit 调用终止它**。

ide 可以安全地假设代码的其余部分不会执行，并将其范围扩大。

```
function no_value_returned(*string* $params): ***never*** {
    ... // code
    exit();
}
```

如果函数没有抛出异常或者终止程序，PHP 将抛出一个 TypeError 异常。

```
function does_not_follow_the_rules(): ***never*** {
    echo 'I do not abide the rules!'
}// Type Error
*TypeError: dispatch(): never-returning function must not implicitly return*
```

# 3.只读属性

终于！声明为只读的类属性**只能初始化一次**，不允许进一步更改。

```
class Human {
    public **readonly** string $dna; public function __construct(string $dna) {
        $this->dna = $dna;
    }
}$human = new Human("...");
```

更重要的是，只读属性**只能在类**中设置，无论是从构造函数还是从另一个方法。

## 和睦相处

虽然 readonly 是一个保留关键字，但是它允许用 readonly 来声明函数，因为一些 PHP 框架声明了它们自己的 readonly 函数

## 什么是不允许的？

*   只读属性只能设置一次。第二次(以及更多次)尝试将导致错误
*   在类的范围之外初始化只读属性
*   **你不能直接设置一个值，增加它，引用和做数组操作，因为一旦设置好，PHP 不允许你修改它**
*   **您不能取消设置只读属性**
*   **没有类型的声明(仅支持类型化属性)**

# **4.新函数—数组列表**

**该函数**返回 true 或 false，并检查给定数组是否是值的语义列表**——这意味着:**

*   **密钥必须是整数**
*   **两者之间没有间隙**
*   **密钥从 0 开始**

**此外，该函数将在空数组上返回 true。**

****array_is_list** **返回 true** 时的例子:**

```
array_is_list([]); *// true*
array_is_list([1, 2, 3]); *// true*
array_is_list(['text', 2, 3]); *// true*
array_is_list(['text', 'sentence']); *// true*
array_is_list([0 => 'text', 'sentence']); *// true*
```

**当 **array_is_list 返回 false** 时的示例:**

```
*// Key does not start with 0*
array_is_list([1 => 'text', 'sentence']); *// false*

*// Keys are not in order*
array_is_list([1 => 'text', 0 => 'sentence']); *// false*

*// Non-integer keys*
array_is_list([0 => 'text', 'foo' => 'bar']); false

*// Non-sequential keys*
array_is_list([0 => 'text', 2 => 'bar']); false
```

# **5.枚举**

**增加的一个额外特性是支持**枚举**。一个例子是用枚举来表示**状态**:**

```
**enum** Status {
    **case** Active;
    **case** InProgress;
    **case** Finished;
}
```

**我们现在可以在函数中接受值时强制类型:**

```
function set_status(**Status** $status) { }
...set_status(**Status::Finished**);
```

# **6.新的类常量— final(关键字)**

**现在，您可以将 **final** 添加到类常量中。在 PHP 类中，你可以覆盖它们子类中的常量。**

**现在可以使用 **final** 关键字禁止这种情况:**

```
class Example {
    **final** public const MYCONSTANT = 1;
}class SubExample extends Example{
    public const MYCONSTANT = 2;
}*Fatal error: SubExample::MYCONSTANT cannot override final constant Example::MYCONSTANT in %s on line %d*
```

**此外，给私有常量添加一个 **final** 标志是行不通的，因为私有属性首先不能在类之外被访问。**

# **7.一级可调用语法**

**引入了新的语法来创建可调用的。**

**不使用 *Closure::fromCallable* (它返回一个**可调用的**，Closure 对象)，下面是新语法的样子:**

```
// OLD SYNTAX
$callable = **Closure::fromCallable('strtoupper')**;
echo $callable('foo'); *// FOO*// NEW SYNTAX
$callable = **strtoupper(...)**;
echo $callable('foo'); // FOO
```

**Callable，后跟 **(…)** 使得一级 callable。**

# **8.纤维**

**PHP 的一个新版本(可能被认为是一个比它实际上更重要的版本)增加了一个**机制来管理并行性**。虽然您可能不会直接使用它，但是一些框架会大量使用它们。**

```
$fiber = new Fiber(function (): void {     
    $valueAfterResuming = Fiber::suspend('after suspending'); /*/ … * 
}); $valueAfterSuspending = $fiber->start();   
$fiber->resume('after resuming');
```

**这应该会给 PHP 带来一个轻量级的、期待已久的**并发**。**纤程**基本上维持自己的堆栈，有四种状态:**

*   **出发**
*   **运转**
*   **暂停的**
*   **终止的**

# **9.数组解包**

**是的，我知道，**这在 PHP 以前的版本**中已经被允许了…但是，它**只适用于数字键**。**

**之前不支持字符串键的主要原因是，对于如何合并数组副本还没有达成共识。**

**RFC 通过使用 **array_merge** 的语义解决了这个问题:**

```
$array1 = ["x" => 1];  
$array2 = ["y" => 2]; 

$array = ["x" => 0, ...$array1, ...$array2]; 

var_dump($array); *// ["x" => 1, "y" => 2]*
```

# **10.fsync 和 fdatasync 功能**

**著名的 **fflush()** 函数和这两个新的坏男孩有一些相似之处。**

**fflush() 在写操作之后使用，以确保 PHP 的内部缓冲区被刷新到操作系统。**

****fsync()** —同步对文件的更改(包括元数据)。非常类似于 *fflush* ，但是它也指示写入存储介质。**

****fdatasync()** —将数据(但不是元数据)同步到文件。**

## **使用**

```
$file = 'test.txt';

$fh = fopen($file, 'w');
fwrite($fh, 'test data');
fwrite($fh, "\r\n");
fwrite($fh, 'additional data');

fsync($fh);
fclose($fh);
```

# **结论**

**PHP 8.1 提供了许多有趣的特性。如果你问我，我甚至会称之为一个主要的版本变化。从过去的几个版本更新来看，PHP 看起来越来越像任何其他现代语言，在性能和语法方面都得到了改进，同时仍然保持了其代码库、社区支持和稳定性。**

**干得好！**