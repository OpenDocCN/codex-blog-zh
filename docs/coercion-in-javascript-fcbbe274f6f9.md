# Javascript 中的强制

> 原文：<https://medium.com/codex/coercion-in-javascript-fcbbe274f6f9?source=collection_archive---------12----------------------->

![](img/1b1151dbdc2bc639d5d79c77d8046ce1.png)

你曾经对这些 javascript 问题的结果感兴趣吗？那

```
[] + [] = ""
[] + {} = "[object Object]"
{} + {} = NaN
{} + [] = 0
[] + 5  = "5"
```

我既好奇又震惊。这就是为什么我深入研究，以了解那里发生了什么。在这篇文章中，我将与你分享我的发现，你将能够看到我们是如何得到这些答案的。

# 数据类型和类型

javascript 中有七种数据类型，分别是:`null, undefined, boolean, string,number,object and symbol`。

`typeof`操作符用于确定 javascript 操作数的数据类型。在`1 + 2`这个表达式中，`1`和`2`都是`operands`。`typeof`返回一个字符串，如下所示:

```
typeof "string"  ==> 'string'
typeof 4         ==> 'number'
typeof undefined ==> 'undefined'
typeof true      ==> 'boolean'
typeof {}        ===> 'object'
typeof Symbol()  ===> 'symbol'
typeof null      ===> 'object'//===================================
typeof []                 // 'object'
typeof function func(){}.  // 'function'
```

注意`null`是唯一的数据类型，它是`falsy`并从`typeof`检查中返回一个‘对象’。

并且`typeof function`和`typeof array`返回‘函数’和‘对象’。原因是`functions`和`array`是`object`类型的子类型。

## 强迫

Javascript 是动态类型的。这意味着变量的类型可以改变。也就是说，一个字符串形式的变量最终可以是另一种类型，比如像这样的对象:

```
let person = "Chidera"  //string data type
  person = {}               // has become an object data type
```

**强制**，也称为“类型转换”，用于“将一种数据类型转换为另一种”。例如，用来将一个字符串转换成一个数字或者将数字转换成字符串等等。

有两种类型的胁迫，即:“`Explicit and implicit coercion`”。

当使用诸如`String(), Number() or Boolean()`这样的语法直接转换类型时，会发生显式强制。

```
let num = 4; //number type
let str = 'person' // string type
let bool = true // boolean type// Explicit conversion syntax gives
String(4) = '4' // number 4 has been converted to string
Number(true) = 1  // boolean true has been converted to number 
Boolean(str) = true // string "str" has been converted to boolean
```

当使用某些操作符和表达式时，隐式强制就不那么明显了。例如

```
let age = 13;
let info = 'The student is' + age 'years old' // The student is 13 years old;
```

除了称为`non-primitive`的`object`数据类型外，所有的数据类型(空、未定义、布尔、字符串、数字和符号)都称为`primitives`。

# 原始强制

原语和非原语的强制行为是完全不同的。原语可以被强制或更改为数字、字符串或布尔值。

# 将原语强制转换为数字

使用`comparison operators`将图元更改为`number`。比较运算符包括“`>, >=, <, =<`”等。让我们看看这是如何做到的

```
// string and number
 "6" >= 1
// Solution,
// - Convert '6' (string) to number gives 6 (number), hence
 6 >= 1 //which returns
 true// boolean and number
false > 3
// Solution,
// - Convert false to number gives 0, hence
0 > 3 // returns
false
```

# 将原语强制转换为字符串

使用一元+运算符将原语强制转换为`string`。如果任何一个操作数是字符串，它会将它们强制转换为字符串。例如，这被称为`operator overboarding`

```
"7" + "hello"
// gives "7hello" because they are both strings7 + "hello"
// gives "7hello" because one of the operands "hello" is a string
```

# 将原语强制转换为布尔值

使用逻辑运算符将原语转换为布尔值。逻辑运算符包括“`&&, ||, !`”。

```
345 && "hello"
// evaluates to 
= true && true
// because converting a number to boolean gives you "true", same as converting a string to boolean also gives you "true"
=  true// Also
true && "hello"
= true && true
= true
```

关于原语强制的更详细的解释，请查看我专门为 javascript 中的[强制原语而写的这篇文章](https://hashnode.com/post/coercing-primitives-in-javascript-ckh4q0phi05mz39s16ptvhgl8)

# 非原始强制(对象强制)

我们已经看到原始的强制是如何进行的。在本节中，我们将对非原语进行同样的操作。回想一下，我们之前说过 object 数据类型称为非原语，for 函数和数组的`typeof`将总是分别解析为“function”和“object ”,因为函数和数组是 object 的*子类型*。就像原语一样，对象或非原语可以被强制为布尔值、字符串或数字。

# 将非基元/对象强制转换为布尔值

逻辑运算符用于将对象强制转换为布尔值。`Objects will always resolve to true`。

```
// if we did
console.log({} && [] && "person") // "person"
// we get "person" because
{} && [] && "person" // resolves to
true && true  && true // finally giving
= true// Also, 
// if we 
console.log({} && true) // "true"
// Same principle above applies
```

# 将非基元/对象强制转换为数字和字符串

将非原语转换为数字和字符串的整体思想就是找到一种将非原语转换为原语的方法。

每个对象原型都有两个影响强制的方法。分别是`valueOf()`和`toString()`。如果您检查它们的类型，您会看到它们解析为如下函数:

```
typeof Object.prototype.valueOf() // "function"
typeof Object.prototype.toString() // "function"
```

现在让我们检查一个对象的`valueOf()`和`toString()`。

```
let myObj = {} 
myObj.valueOf() 
myObj.toString()
```

上述结果将构成本部分评估答案的基础。要将对象更改为数字或字符串，javascript 引擎将首先执行一些检查:

1.  它将根据对象的 DefaultValue 内部方法调用对象上的 valueOf()或 toString()。有人说是基于传递的“暗示”。如果数字是提示，则调用 valueOf，否则如果字符串是提示，则调用 valueOf。在大多数情况下，首先调用 valueOf。
2.  接下来要检查(1)中的返回值是否是原语。如果是原语，则返回原语，否则继续(3)。
3.  调用 next 或 fallback 方法。这可以是 toString()或 valueOf()。也就是说，如果在上面的(1)中调用了 valueOf()，那么下一个或 fallback 方法将变成 toString()，同样，如果在(1)中调用了 toString()，那么 fallback 方法将变成 valueOf()。
4.  使用回退方法再次执行(1)。如果返回值是一个原语，则返回它，否则抛出一个错误“`TypeError: Cannot convert object to primitive`”。

回想一下上面的例子，我们检查了对象的 valueOf 和 toString()。利用这些结果，我们来看一些例子

**例 1**

```
let myObj = {}
2 + myObj
```

2 已经是一个原语，所以让我们来评估非原语。首先我们在 myObj 上调用 valueOf()。

```
myObj.valueOf()
```

但是它解析为{}，这也是非 primitve。因此我们调用回退方法，在本例中是 toString()。

```
myObj.toString() //"[object object]"// we now have
 2 + "[object object]"
```

`toString()`返回一个原始的字符串。

召回`operator overboarding`？，如果任何操作数是字符串，它将整个表达式计算为字符串。在上面的块中，“[object object]”是一个字符串。所以我们有

```
= "2" + "[object object]" // "2[object object]"
```

因此，

```
2 + myObj // "2[object object]"
```

让我们看另一个例子

**例 2**

```
[] + 5
```

*一元运算符(+)根据参数类型*执行字符串连接或数字加法。

因此，+ 5(参数类型)变成了 5(数字)。根据(1)中的规则，Number 现在是求值的提示，所以我们在非原语[]上应用 valueOf()

```
[].valueOf() // []
```

它返回一个数组，另一个非原语。让我们回到下一个方法，即 toStirng()。

```
[].toString() //""
```

它计算出一个空字符串，这是一个原语，所以我们有

```
[] + 5 
// as
"" + 5
```

因为其中一个是字符串

```
"" + 5 //becomes
"" + "5" // "5"
```

我们的最终解决方案是在评估两个字符串后得到的

```
[] + 5 // "5"
```

**例 3**

```
[] + {}
```

首先我们对它们调用 valueOf()。

```
[].valueOf() // []
{}.valueOf() // {}
```

返回了非原语，因此我们调用了 fallback 方法，在本例中是 toString()

```
[].toString() // ""
{}.toString() // "[object object]"
```

现在我们有了这样的字符串原语

```
"" + "[object object]"
```

评估两个原语最终给我们

```
"[object object]"
```

因此

```
[] + {} //"[object object]"
```

**例 4**

```
[] + []
```

对它们使用 valueOf()会给出

```
[].valueOf() // []
[].valueOf() // []
```

因为返回了非原语，所以我们使用回退方法，即 toString()。

```
[].toString() // ""
[].toString() // ""
```

我们的问题归结为

```
"" + "" // ""
```

因此

```
[] + [] //""
```

**例 5**

```
{} + []
```

现在这个有点棘手，因为它解析为 0。让我们看看怎么做。

回想一下，当我们在例 3 中评估`[] + {}`时，我们得到了`"[objectobject]"`

然而，对于我们的第五个例子，考虑两个操作数{}和[]。对于第一个{}，javascript 不会将其解释为对象。相反，它将其解释为一个代码块。所以我们有这样的东西

```
{} + [] // as
code block + []
```

javascript 接下来要做的是忽略代码块，所以我们有

```
+ []
```

[]上的一元运算符(+)将其强制为一个数字，因此我们有

```
Number([]) // 0
```

我们就是这样回到 0 的。

概括起来

```
{} + []
= code block + []
```

忽略我们拥有的代码块

```
+ []
```

使用一元+评估[]

```
Number([]) // 0
```

**例 6**

```
{} + {}
```

与示例 5 一样，javascript 将第一个操作数{}解释为代码块。它忽略了它，并且由于一元(+)操作符，它像这样处理第二个

```
Number({}) // NaN
```

因此

```
{} + {} // NaN
```

**例 7**

```
'' - false
```

减法操作数没有溢出操作，因此对操作数调用 ToNumber

```
Number('') - Number(false) //
// This resolves to
 = 0 - 0
```

因此

```
'' - false // 0
```

*原载于*[*https://dera . hashnode . dev*](https://dera.hashnode.dev/coercion-in-javascript)*。*