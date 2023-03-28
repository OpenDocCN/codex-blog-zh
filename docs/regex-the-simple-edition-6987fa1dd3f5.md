# 正则表达式:简单版

> 原文：<https://medium.com/codex/regex-the-simple-edition-6987fa1dd3f5?source=collection_archive---------4----------------------->

[](https://www.linkedin.com/in/jason-padilla-8294bb1a7/) [## Jason Padilla -美国纽约布鲁克林区马西实验室学校| LinkedIn

### 查看 Jason Padilla 在全球最大的职业社区 LinkedIn 上的个人资料。杰森的学历列在…

www.linkedin.com](https://www.linkedin.com/in/jason-padilla-8294bb1a7/) ![](img/d7aff7b0de2e41d3540f4d797cfaf807.png)

Regex 也称为正则表达式，是各种编程语言中使用的模式，它匹配称为字符串的原始数据类型的一部分。如果你认为模式已经很难在代码之外找到了，那么你现在会猛然醒悟。但是不要担心，这就是你在这里的原因；学习复杂主题的基础知识。但是让我提醒你，这是正则表达式的简化版本。这意味着您必须继续阅读这个主题，但是不要担心，因为继续学习正则表达式的一个好地方是 freeCodeCamp.org。无论如何，让我们开始你的旅程。

# 介绍

一位名叫肯·汤普森的数学家在 1986 年创造了正则表达式。他从斯蒂芬·克莱尼、沃伦·s·麦卡洛和沃尔特·皮茨那里汲取灵感。尽管 regex 看起来似乎是一个简单的概念，但它有几个关键部分必须掌握才能发挥其威力。在本文中，我们将只讨论五个部分:字符类、锚、量词和交替。对于本文，使用的编程语言是 JavaScript。

# 停下来。

我忘记提到最关键的部分，regex 的结构以及如何使用这个模式。要开始一个模式，您必须首先创建该模式。你用两个像/ /这样的正斜杠来做这件事。我们称之为`search pattern`。为了找到一个精确的匹配，您只需在搜索模式之间放入一个字符串，就像这样`/Jason/`。默认情况下，它只会找到第一个匹配的`Jason`。但是，您可以通过标志来改变搜索模式的行为。我们将使用两种类型的旗帜，`-g`和`-i`。通过执行`/Jason/g`，搜索模式现在将在一个字符串中查看`Jason`的多个条目。

```
let string = 'Jason Padilla';
let literalMatch = /Jason/g;

const test = literalMatch.test(string);
const match = string.match(literalMatch);

console.log(test) // true
console.log(match) // [‘Jason’]
```

现在将`-i` 添加到搜索模式:`/Jason/gi`。这种搜索模式不再区分大小写，这意味着无论字符串是否有类似于`JASON`或`jAson`的`Jason`，该模式仍然会匹配它。

```
let string = 'Jason jAson JASON';
let regex = /Jason/g;

const match = string.match(regex);

console.log(match) // [‘Jason’]

let string = 'Jason jAson JASON';
let regex = /Jason/gi;

const match = string.match(regex);

console.log(match)  // [‘Jason’, ‘jAson’, ‘JASON’]
```

还有更多的特殊人物要讲，但节目必须继续。为了继续这个节目，你必须看看正则表达式是否有效。我们可以用`.test()`方法做到这一点。该方法获取正则表达式，将其应用于一个字符串，并根据是否找到匹配返回`true`或`false`。还有另一种称为`.match()`的方法，它创建一个新数组，提取匹配项，并将它们放入新数组中。现在我们开始吧。

![](img/6bf52a0be4e32590968c3b5882074485.png)

# 字符类别

首先，字符类很简单，它允许计算机区分字符。这方面的一个例子是可以辨别字母和数字或只有数字。为了只匹配数字，您将输入搜索模式`/\d/g`，并添加`-g`标志来启用迭代。

```
let numbers = "1234567890";
let regex = /\d/g;

const match = numbers.match(regex);

console.log(match) // [‘1’, ‘2’, ‘3’, ‘4’, ‘5’, ‘6’, ‘7’, ‘8’, ‘9’, ‘0’]
```

要匹配字母和数字，您可以在搜索模式中输入`\w`来匹配任何字符(字母数字和下划线)。

```
let numbers = "A B C, It's easy as 123";
let regex = /\w/g;

const match = numbers.match(regex);

console.log(match)  // ['A', 'B', 'C', 'I', 't', 's', 'e', 'a', 's', 'y', 'a', 's', '1', '2', '3']
```

你也可以用大写的变体做相反的事情；`\D`匹配非数字的所有内容，而`\S`匹配除空白、换行符和制表符之外的所有内容。

```
let numbers = "A B C, It's easy as 123";
let regex = /\D/g;

const match = numbers.match(regex);

console.log(match) // ['A', ' ', 'B', ' ', 'C', ',', ' ', 'I', 't', "'", 's', ' ', 'e', 'a', 's', 'y', ' ', 'a', 's', ' ']

let numbers = "A B C, It's easy as 123";
let regex = /\S/g;

const match = numbers.match(regex);

console.log(match) // ['A', 'B', 'C', ',', 'I', 't', "'", 's', 'e', 'a', 's', 'y', 'a', 's', '1', '2', '3']
```

关于角色类的另一个很酷的事情是方括号`[]`。这允许您搜索想要匹配的特定字符组。为了进一步增强这一点，您可以通过使用连字符`-`来定义想要匹配的字符范围。我们可以在下面看到一个例子。在这个例子中，我想匹配所有以`at`结尾的字符。这是通过定义所有字母字符到`[a-z]`的范围并在末尾添加‘at’作为我们想要的结束字符的精确匹配来实现的。

```
let numbers = "That rat ran with my food. So I tried to hit it with a bat. Afterwards I laid on the mat while he ate the food on my doormat";

let regex = /[a-z]at/g;

const match = numbers.match(regex);

console.log(match) // ['hat', 'rat', 'bat', 'mat', 'mat']
```

# 锚

锚点在正则表达式中很特殊，因为它们不匹配特定的字符；它们匹配字符的位置。`^`锚点匹配文本开头字符的位置。`$`锚点匹配文本末尾字符的位置。

```
let numbers = "The story never ends, it just has new beginning";
let regex = /^The/g;

const match = numbers.match(regex);

console.log(match) // ['The']

let numbers = "The story never ends, it just has new beginning";
let regex = /beginning$/g;

const match = numbers.match(regex);

console.log(match) // ['beginning']
```

# 量词和交替

量词指定字符串中需要匹配的字符数。对于本节，我们将只通过使用花括号`{}`来讨论数量说明符。花括号有两部分，下面的数字和上面的数字。在匹配一个字符串或另一个字符串的逻辑 OR 运算符`|`之后进行替换。

```
let numbers = "abcdefghijklmnopqrstuvwxyz";
let regex = /[a-z]{10}/g;

const match = numbers.match(regex);

console.log(match) // ['abcdefghij', 'klmnopqrst']

let numbers = "Here at Marcy Lab, we do both frontend and backend development";
let literalMatch = /frontend|backend/g;

const match = numbers.match(literalMatch);

console.log(match) // ['frontend', 'backend']
```

# 抛售

![](img/a0f3385f4121abbf872d5c3f13c5e20d.png)

正则表达式根本不是一个容易学的题目。我特别遗漏了 regex 的许多组件，因为它们深入到了正则表达式的功能中；其中一些是正面/负面环视、捕捉组等。请记住，本文是对 regex 的简单概述，因此继续学习其他平台对您最有利。我推荐的一些资源有 freeCodeCamp(我从中汲取灵感的地方)、Odin Project、MDN 等等。记住，编码是一种通过无数小时的学习和工作而学到的技能；所以，当时间或手头的话题变得艰难时，不要感到气馁。继续前进，感谢您花时间阅读我的文章。