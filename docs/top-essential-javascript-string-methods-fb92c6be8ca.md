# 最重要的 Javascript 字符串方法

> 原文：<https://medium.com/codex/top-essential-javascript-string-methods-fb92c6be8ca?source=collection_archive---------13----------------------->

## 已知和鲜为人知的字符串方法指南。

![](img/5a15632ab6cedbbc21f44ed62857a921.png)

当使用 Javascript 或任何现代编程语言时，有几件事是你经常要做的，那就是使用变量名和字符串。

字符串对于保存以文本形式表示的数据最有用。对字符串最常用的操作是检查字符串长度的操作，使用+(=)或文字运算符构建和组合字符串的操作，使用 indexOf()检查子字符串的存在或位置的操作，或者使用 substring()方法提取子字符串的一部分的操作。也可以使用。trim()和多个操作的组合来大写一个单词。在 Javascript 中不是标准的，但通过组合操作很容易创建的东西。

# 让我们看看我们有什么

首先，让我们快速地看看如何创建字符串。我会很快地看一遍，如果有必要的话，以后可能会写一篇适当的文章。这绝不是详尽无遗的，还有许多其他方法可以做到这一点。但是对于本文来说，这样做就很好了。

Javascript 中的字符串基本上是单引号、双引号或模板文字引号之间的所有内容。

例如:

也可以创建一个字符串，因此它返回一个 string 对象。但是这超出了本文的范围。

因为您现在创建了一个字符串变量(例如，上面例子中的`***single***`)，所以您可以对它们进行操作。您可以使用方法来完成这些工作。喜欢

`const everythingUppercase = single.toUpperCase()`

它将获取变量`***single***`中的任何内容，并将所有字母转换成大写字母。

## 长度

可以说最常用的操作是获取字符串的长度。技术上来说`length`不是方法而是属性。

例如，这对于验证用户的输入没有超过某个限制是有用的。

如果字符串为空，将返回 0。请记住，空格也是一个字符。所以。`“ “.length`会回一句

## 包括和索引

接下来是检查字符串是否包含特定子串的操作。或者说，如果一个句子包含某个单词。这可以通过几种方式来实现。这取决于您需要支持哪些浏览器(如果有)。使用`includes`(IE 不支持)或`indexOf`(任何地方都支持)

现在推荐使用 includes 搜索子字符串。但是，它与 Internet Explorer 不兼容。

当你需要支持 ie 浏览器时，使用`indexOf`。

indexOf 返回要搜索的字符串中搜索词的第一个匹配项的索引(位置)。如果没有找到，则返回-1。因此，要检查一个单词是否存在于一个句子中，您可以检查是否返回-1

## 薄片

Slice 从给定的起始位置返回字符串。或者，当使用负数作为第二个参数时，在尾部切掉。

要从一个单词或句子中切下一段，你也可以用我们刚刚学过的东西的组合。使用子串和长度，我们可以执行以下操作:

因为对这两种方法的支持都非常好。这两种方法返回边缘情况的方式不同。但它们大部分是一样的。。切片看起来简洁多了；我想你会同意的。

## 改变大小写

改变大小写很容易。Javascript 在字符串上有一个`toUpperCase()`和一个`toLowerCase()`方法。所以:

将返回全部大写的单词。反之 toLowerCase()将返回小写的单词 all。

## 整理

当字符串的开头或结尾(或两者)有多余的空格时，可以使用`trim(), trimEnd(), and trimStart()`

`trim()` 删除开头和结尾的所有空白。
`trimEnd()`只修剪掉字符串末尾的空白。
`trimStart()` 只从字符串的开头开始修剪空白。

## 替换

替换这样的作品:

`sentence.replace(‘find this’, ‘replace with this’)`

这对于只需要替换一个单词的简单搜索和替换操作来说非常容易。但是如果你想替换所有的事件，你必须做的有点不同。使用 RegExp 或新的 replaceAll。

方法中的第一个参数是“find this ”,它可以包含 RegExp 作为匹配的模式。因此，无论正则表达式匹配什么，它都将被替换。替换上面示例中所有出现的“the”。你能做到的。

`sentence.replace(/the/g, ‘a’) // a quick brown fox jumps over a lazy dog`

您不需要将第一个参数(引号内的模式)括起来，因为它不是字符串，而是正则表达式类型。

RegExp 本身就是一个主题，远远超出了本文的范围。你可以在这里了解更多信息:

[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Guide/Regular _ Expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions)

一种新的方法是使用`replaceAll.`小心，但是这在老版本的浏览器(例如 Chrome -85，FF -77)上不起作用

这将用*替换所有出现的*

## **将单词或句子大写**

**如果你想大写一个单词或句子，可以结合使用这些操作。因为您可以将这些一个接一个地组合起来，所以有一种方法如下:**

**这里要注意的一点是`word[0]`。这将返回字符串的第一个字母。(因为索引总是从零开始计数)。所以我会返回第二个字母，以此类推。例如，`word[1]`将返回`e`，这与数组的工作方式相同。(如果您的浏览器不支持这种字符访问方式，您可以使用`.charAt(0)`来代替)。**

**另一件要知道的事情是，你可以使用`+`操作符来组合字符串。**

**`word + word // hellohello`**

**因为您可以组合操作，操作`toUpperCase()`将在单词“hello”的第一个字母上执行，所以字母`**‘h’**`变成了`**‘H’**`**

**一个接一个，操作如下:**

1.  **使用`word[0] or word.charAt(0)`获取单词的第一个字母**
2.  **使用`toUpperCase()`将该字母转换成大写字母**
3.  **使用`+ word.slice(1)`将单词的剩余部分添加到其后**

**这些是 Javascript 中字符串操作的基础。还有许多可用的方法和属性，了解它们的最好方法是 MDN Web 文档中关于字符串的内容。**

**[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String) [## 字符串- JavaScript | MDN

### String 对象用于表示和操作字符序列。字符串对于保存数据很有用…

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 

*****牛逼的是你还在读书。我想做一个完整的微型教育形式的网站开发初学者指南。如果你感兴趣，请告诉我。订阅/鼓掌感谢阅读。*****