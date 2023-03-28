# javascript 中的强制原语

> 原文：<https://medium.com/codex/coercing-primitives-in-javascript-4546f4d52a15?source=collection_archive---------18----------------------->

![](img/092ae3ff858112bd82a5321909587bba.png)

# 理解强制

javascript 中有七种数据类型，即:`null, undefined,string,number,symbol, boolean`和`object`。前六个叫做`primitives`。最后一个，Object 叫做`non-primitive`。

Javascript 是动态类型的。这意味着一个变量可以以一种类型(例如字符串数据类型)开始，以另一种类型(比如数字)结束。例如

```
let person = "human"  //string data type
  person = 10               // has become a number data type
```

**强制**，也称为“类型转换”，用于“将一种数据类型转换为另一种”。例如，用来将一个字符串转换成一个数字或者将数字转换成字符串等等。

图元可以被强制或更改为`Number, String`或`Boolean`。

# 将原语强制转换为数字

使用`comparison operators`将图元更改为`number`。比较运算符包括“`>, >=, <, =<`”等。让我们看看这是如何做到的

**例 1:数字串和数字**

```
"6" >= 1
//Solution,
//Convert '6' (string) to number gives 6 (number), hence
 6 >= 1 // true
 true
```

**例 2:布尔与数字**

```
false > 3
//Solution:
//Convert false to number gives 0, hence
0 > 3 // false
false
```

**例 3:空值和数字**

```
null > 4 
//Solution:
//Convert null to number gives 0, hence
0 > 4 // false
false
```

**例 4:文本串和数字**

```
'hello' > 4 
//Solution:
//Convert 'hello' to number gives 1, hence
1 > 4 // false
false
```

**例 5:未定义和编号**

```
undefined > '9' 
//Solution:
//Convert undefined to number gives NaN, hence
NaN > 4 // false
false
```

**示例 6:布尔和数字字符串**

```
true > 4 
//Solution:
//Convert true to number gives 1, hence
1 > 4 // false
false
```

# 将原语强制转换为字符串

使用`unary + operator`将图元强制转换为`string`。如果任何一个操作数是字符串，它会将它们强制转换为字符串。这叫`operator overboarding`。

**例 1:布尔+字符串+数字**

```
true + "Is" + 5 // "trueIs5"
```

**例 2:字符串+空值**

```
"hello" + null // "hellonull"
```

**例 3:字符串+布尔**

```
"hello" + true // "hellotrue"
```

**例 4:字符串+未定义**

```
"hello" + undefined // "helloundefined"
```

**例 4:数字+字符串**

```
4 + "hello" // "4hello"
```

# 将原语强制转换为布尔值

使用逻辑运算符将原语转换为布尔值。逻辑运算符包括“`&&, ||, !`”。评估基于逻辑运算符。

# 使用 and(&&)运算符的示例

**例 1:号和号**

```
345 && 567 // 567 
//resolved as
true && true
567
```

**例 2:数字和字符串**

```
345 && "hello" // "hello" 
//resolved as
true && true
"hello"
```

**例 3:空值和字符串**

```
null && "hello" // null 
//resolved as
false && true
null
```

**例 4:弦与弦**

```
"person" and "hello" // "hello" 
//resolved as
true && true
"hello"
```

**示例 5:空值和布尔值**

```
null && true // null
//resolved as
false && true
null
```

**例 6:数字和布尔**

```
4 && true // true
//resolved as
true && true
true
```

**示例 7:空且未定义**

```
null && undefined // null
//resolved as
false && false
null
```

# 使用 or(||)运算符的示例

**例 1:编号||编号**

```
345 || 567 // 345 
//resolved as
true || true
345
```

**例 2:数字||字符串**

```
345 || "hello" // 345
//resolved as
true || true
345
```

**例 3:空||字符串**

```
null || "hello" // "hello" 
//resolved as
false || true
"hello"
```

**例 4:字符串||字符串**

```
"person" || "hello" // "person"
//resolved as
true || true
"person"
```

**示例 5: null || boolean**

```
null || true // true
//resolved as
false || true
true
```

**例 6:数字||布尔**

```
4 || true // 4
//resolved as
true || true
4
```

**例 6:空||未定义**

```
null || undefined // undefined
//resolved as
false || false
undefined
```

# 结论

javascript 的动态特性使得数据类型可以从一种类型转换为另一种类型，而强制正是实现这一壮举的机制。原语可以被强制为布尔值、数字或字符串。

关于强制的更详细解释，您可以阅读这篇关于 javascript 中[强制的姊妹文章。](https://hashnode.com/post/coercion-in-javascript-ckh2i0wt30319ajs1e88ygnxv)

*最初发布于*[*https://dera . hashnode . dev*](https://dera.hashnode.dev/coercing-primitives-in-javascript)*。*