# 最重要的 Javascript 数字方法

> 原文：<https://medium.com/codex/top-essential-javascript-number-methods-840d68ebb633?source=collection_archive---------17----------------------->

## 你可能还不知道这些超级方便的数字方法。

![](img/73c7a524462cf66870100d73d70a2845.png)

数字类型的属性和方法并不是我每天都要做的事情，对我来说，这是一个比较热门的话题。这通常是因为一些没有加起来的东西，比如在比较浮点数的情况下——这总是让我感到惊讶，我浪费了一些时间来研究数字主题。

但是它们在提供一种简单的基数转换方法(`toString`)、添加货币类型或者基于某人可能访问的地区坚持某种数字格式(`toLocaleString`)方面非常有用

## 最大值和最小值

机器中的数字有一个特定机器(在这个例子中是 Javascript VM)可以操作的最大和最小大小。在 Javascript 中，这些最小值/最大值作为常量存储在 Number 类型中。现在还不清楚如何以及为什么使用它们。

## 希腊语字母之第五字

EPSILON 用于返回接近零的最小正数。

一个用例是检查浮点数的相等性。说你要查`0.1 + 0.2 = 0.3`。在你看来，这是可行的。在 Javascript 中，这不会累加起来(双关语),因为 Javascript 中没有无限的精度，在某些时候，精度的缺乏会泄漏到结果中。

`console.log(0.1 + 0.2) //0.30000000000000004`

## 最大安全整数

这代表了 Javascript 中最大的安全整数。`9007199254740991`这是你能创建的最大整数。

我们可以用 BigInt tho 扩大规模。但那是完全不同的话题。您可以在这里找到关于 BigInt 的更多信息:

[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) [## BigInt - JavaScript | MDN

### bigint 是一个内置对象，它的构造函数返回一个 BigInt 原语——也称为 BigInt 值，或者有时…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt) 

另一个 MAX_VALUE，MIN_SAFE_INTEGER，MIN_VALUE 大多是理论上的，平时不会碰到。但是它们可以以与上述大致相同的方式使用。最后两个确实有实际用途。虽然不太明显。

## 负无穷大和正无穷大

`Number.NEGATIVE_INFINITY`和`Number.POSITIVE_INFINTE`是一个特殊的数值，当一个运算或数学函数产生一个大于最大或最小可表示数的负值或正值时返回。

JavaScript 将`NEGATIVE_INFINITY`值显示为`-Infinity`，将`POSITIVE_INFINITY`值显示为`Infinite`，该值在数学上表现为无穷大；比如任何东西乘以无穷大就是无穷大，任何东西除以无穷大就是零。

## 楠还是不是一号

当对数字类型的运算返回数字以外的值时，或者当算术运算生成的值导致*未定义的*或*不可表示的*值时，返回 n an。伊斯南检查南的存在。

MDN 的一些例子

## to 指数

方法将一个给定的值转换成指数符号。

## toFixed

toFixed 可用作 toExponential 的计数器方法。例如，将指数转换回整数。

MDN 提供的示例

## isInteger

要检查某个值是否为整数，可以针对 isInteger 进行测试

## 最高精度

返回指定精度的值。

MDN 提供的示例

## 托洛卡斯特灵

以特定于语言的表示形式返回字符串。这对于超出正常拉丁字符集的多语言网站来说非常方便。而且在不同的地区和不同的数字符号之间的转换也非常方便。

toLocaleString 上有几个方便的选项来进一步指定返回值。例如，这对于需要添加货币符号的国际化非常方便。

## toString

这将返回一个数字的字符串表示形式。使用可选的底座。

基数可以在 2 到 36 之间。超级方便的转换，例如，十六进制和十进制。`(255).toString(16) // ff`或十进制和二进制`(6).toString(2) // 110`

# 关于新的(ES2021)数字分隔符的一些信息

一种新的分隔数字的方法是使用 _ number 分隔符。这对数字的值没有任何影响。这使得阅读大数字变得更容易

`144789320`可以写成`144_789_320`

我提到这一点是因为 Number 不支持数字分隔符。

`Number(‘123_333’) returns NaN`

要使用数字分隔的数字，您应该首先删除 _，例如通过执行以下操作。

`let num = Number(‘123_444_222’.replaceAll(‘_’,’’))`

***厉害了你还在读书。我想做一个完整的微型教育形式的网站开发初学者指南。如果你感兴趣，请告诉我。订阅/鼓掌感谢阅读。***