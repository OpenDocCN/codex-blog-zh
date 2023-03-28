# 如何设计自己的编程语言| Sea #0

> 原文：<https://medium.com/codex/how-to-design-your-own-programming-language-sea-0-d68e240b6155?source=collection_archive---------16----------------------->

![](img/333687ac0e4968107d9dcb2992861265.png)

丹尼尔·伊德里在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

欢迎光临！这是我计划撰写的关于我的编程语言——Sea 的系列文章的第一篇。在这些文章中，我计划记录我编写该语言的过程，以服务于几个主要目标:

*   教你创建编程语言的过程
*   来帮助我更好地学习这个过程和开发 Sea
*   为 Sea 的设计选择创建文档
*   花时间去正确地设计 Sea

注:我写编程语言的经验来自于在 Sea 和 [CodePulse 的编程语言教程](https://www.youtube.com/watch?v=Eythq9848Fg)工作时的亲身经历。该系列教程中有很多非常好的材料，但是代码很糟糕；有一些文件长达数百行。所以，我还是相信我的系列文章会有用的。我将把它们保存到我的[编程语言列表](https://davidjmacdonald.medium.com/list/how-to-create-a-programming-language-0ee2bcc7bccd)中。

这个系列也是我的文章[编程语言的基本历史和如何创建你自己的](/codex/a-basic-history-of-programming-languages-and-how-to-write-your-own-c05b804dd932)的延伸。在那篇文章中，我回顾了编程语言的历史发展，并从概念上解释了创建编程语言的一般过程。在这个系列中，我将详细介绍每个步骤。你可以完全照搬我的做法，或者用它来创造你自己的语言。请注意，我将在前面描述的三个步骤中添加第四个步骤:预处理。这是因为 C 有一个预处理器，它提供了一些好处。但是现在，在以后的文章发表之前，我将忽略预处理器。

![](img/c8498ad538f330164a8ffea78dfb98ef.png)

照片由 [Edho Pratama](https://unsplash.com/@edhoradic?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 目的和设计理念

关于 Sea 的想法很简单。我花了很多时间用各种语言编程，但我个人最喜欢的语言是 Python。我花了一段时间几乎精英化地思考“Python 是初学者的语言。括号并不可怕；我对 Java 和 C++没问题。然而，一旦我开始真正迷上 Python，我就爱上了。由于简单的语法，Python 只是一种乐趣。调试要容易得多，总体来说阅读代码也很容易。我认为将计算机语言推向会话语言是理想的。

Python 是一门伟大的语言，但它是一门高级编程语言。你*可以*使用 Cython 的低级功能，但就我而言，这更像是一个黑客而不是一个功能。在 Python 中并没有像在 c 语言中一样有效地使用指针这样的东西的方便和明显的方法。我也非常清楚，并不是每种语言都应该用于所有的事情。一个好的开发者不会简单地选择他们最喜欢的语言，并将其用于网页开发、游戏开发和应用程序开发；一个优秀的 web 开发人员会使用最适合手头工作的工具。所以，如果有一种语言可以做任何事情，那不是很好吗？它将是一种模块化语言，可以非常轻松地用于操作系统等低级应用程序，它将具有高级功能，允许它用于 web 开发和一般应用程序开发，最后，它需要具有简单可读的语法。

那是我梦想的语言。然而，我认为作为我的第一语言，这将是一个太大的任务。所以，我开始想得更小，想出了 Sea——是 C，只是写法不同。**我们的目标是创建一个语法类似于 Python 的 C 语言版本，而不会增加大量的运行时成本。在大多数情况下，我更喜欢缩进语法而不是括号语法。无论如何，好的代码应该是缩进和间隔的，所以使它成为必需的并不是一个可怕的想法。主要的焦点应该是可读性。我用 Sea 实现这一点的方法是将 C 的一般语法 then 化，然后创建 sugar 的语法；语法是做你已经能做的事情的更好的方式。**

Sea 的目的是成为一个对初学者友好的 C 语言版本。它应该和 C 语言一样强大，并且没有显著的运行时开销。因此，应该有语法糖用于模拟某些更高级的功能。这样做将增加 Sea 的编译成本，但不会增加大量的运行时成本。Sea 需要在 Python 和 c 之间取得平衡。考虑到这些因素，我们可以开始设计语言了。

![](img/3e7ae750b051a31a6701277031599199.png)

照片由[瑞恩·华莱士](https://unsplash.com/@accrualbowtie?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 句法和普通语法

在我看来，为一门语言创造语法是这个过程中最难的部分。一种语言包含了太多的部分，很难把它们都识别出来。我认为我们最好的选择是从一门你熟悉的语言开始，然后随着你的进展不断修改。因为 Sea 实际上只是 C 的重新设计，所以语法基本上是一样的。然而，该语法将主要来源于 Python。所以，我准备参考[微软的 C 语言语法总结](https://docs.microsoft.com/en-us/cpp/c-language/c-language-syntax-summary?view=msvc-160)。在这里面，c 有一个深入的语法模型。我将在一定程度上模仿他们的[定义和惯例](https://docs.microsoft.com/en-us/cpp/c-language/definitions-and-conventions?view=msvc-160)。推荐看页面(真的很短)。因为 Medium 没有下标，所以我将使用正则表达式“？”对于可选组件。

请注意，当我写这些的时候，我还没有完全写完 Sea。也就是说，我可能不会把所有的 C 语法都 Pythonify 化。因此，当我开发 Sea 时，我可能不得不回来修改这篇文章。

## 代币

记号是我们语言的基础。每个语法元素都将被表示为一个标记。我们的令牌将是以下类型之一:`*keyword*`、`*identifier*`、`*constant*`、*、*、`*string-literal*`和`*punctuator*`。

`*keyword*`:Sea 中所有保留的关键字，如“if”、“else”、“struct”等。

`*identifier*`:所有用户输入的名称，如变量、函数和结构。它们由下划线、英文字母和数字组成。但是，标识符不能以数字开头。

`*constant*`:这些都是常量数据，不可修改，编译时已知。又进一步分为`*integer-constant*`、`*floating-constant*`、`*enumeration-constant*`、`*character-constant*`、*。*

*   `*integer-constant*`:就我们的目的而言，所有的整数都是十进制的，由十进制数字组成。我将在以后实现不同的数字基础。
*   `*floating-constant*`:暂时为十进制，由十进制数字组成，最多一位小数。我将在以后实现不同的数字基础以及科学记数法。
*   这些将是代表 Sea 中枚举的标识符。
*   `*character-constant*`:海中任意角色。它们将是单引号中的单个字符，或者该字符可能以反斜杠为前缀。

`*string-literal*`:所有字符串或字符的隐式数组。我将添加不同的编码类型和花哨的字符串类型，如 Python 风格的 f 字符串。

`*punctuator*`:所有通用语法符号。这些包括操作符和一般的“标点符号”。在 Sea 中，缩进和换行符是标点符号。

这些是我们出海的信物！你的语言可能有不同的符号，或者可能以不同的方式来表示这些符号，但这没关系。现在，让我们看看这些标记将如何被用来构建我们语言的语法。

![](img/c465a7ab5229230dd4a19b6393e52d5c.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 语法

回想一下，编程语言的语法定义了应该如何一起使用标记来创建顺序或理解。值得注意的一点是，由于 Sea 使用 Python 风格的缩进，我们不会像在许多语言中那样忽略所有空白；然而，我们将忽略第一个非空白字符后的单个空白字符，如果它们不是换行符的话。我们也将忽略空的换行符，这些换行符只是为了让代码更易读。我们的语法将由表达式、声明、陈述和外部定义组成。

## 公式

表达式是解析为一个值的代码行。做好准备，因为下面的语法可能会让你不知所措。这些表达式通常也是递归的。不同类型的表达式之间的运算顺序是从上到下的。但是，表达式中的每个元素都与同一元素中的其他元素具有相同的优先级。这就是在 PEMDAS(或 BODMAS)，括号的顺序最高，其次是指数，然后是乘法**和**除法。很多人都弄错了这一点，但是乘除具有相同的优先级，必须从左向右执行。最后，加法**和减法**的优先级最低，但它们的优先级相同。然而，在编程语言中，这必须谨慎进行。对于语法的递归定义，首先搜索更复杂的定义是很重要的。否则，它可能只能检测到最简单的定义。

`*primary-expression*`:所有表达式的绝对基础。可以采取以下形式:

*   `*identifier*`
*   `*constant*`
*   `*string-literal*`
*   `**(**` `*expression*` `**)**`
*   `*generic-selection*`

`*generic-selection*`:允许泛型类型的函数映射。

*   `**generic**`**`**(**`**`*assignment-expression*`*`**,**`*`*generic-assoc-list*`*`**)**`*******

*******`*generic-assoc-list*`:定义通用函数的关联列表。*******

*   ******`*generic-association*`******
*   ******`*generic-assoc-list*` `**,**` `*generic-asociation*`******

******`*generic-association*`:函数的类型和代码之间的关联。******

*   ******`*type-name*``**:**`******
*   ******`**default**` `**:**` `*assignment-expression*`******

******`*postfix-expression*`:包含后缀运算符的表达式。******

*   ******`*primary-expression*`******
*   ******`*cast-expression*`******
*   ******`*postfix-expression*`*`**[**`*`*expression*`*`**]**`*********
*   *******`*postfix-expression*`*`**(**`*`*argument-expression-list*`？`**)**`*********
*   ******`*postfix-expression*`*`.`*`*identifier*`********
*   ******`*postfix-expression*`*`**->**`*`*identifier*`********
*   ******`*postfix-expression*`T53******
*   ******`*postfix-expression*` `**--**`******
*   ******`**(**` `*type-name*` `**)**` `**{**` `*initializer-list*` `**}**`******
*   ******`**(**``*type-name*``**)**``**{**``*initializer-list*``**,**`******

******`*cast-expression*`:从一种类型转换到另一种类型的表达式。注意，我的造型是 Python 函数调用造型的风格。但是，它会表现得更像 C 的选角。尽管如此，我还是把它和函数调用放在同样的优先级。******

*   ******`*postfix-expression*`******
*   ******`*type-name*`*`**(**`*`*cast-expression*`*`**)**`*********

*******`*argument-expression-list*`:参数列表。*******

*   ******`*assignment-expression*`******
*   ******`*argument-expression-list*` `**,**` `*assignment-expression*`******

******我想暂停一会儿，让大家喘口气，并解决几个问题。在很大程度上，所有这些都是直接来自 C。由于我还没有编写过 Sea，很可能有些语法太像 C 了，我必须修改它，使它更像 Python。当我这样做的时候，我会试着回来更新这篇文章。请再次记住，您的语言可能不需要所有这些。我认为从零开始，这就是我所做的，并不是语言语法的理想策略。我建议你继续遵循这个 Sea 语法，弄清楚每个部分的意思，并确定你的语言是否需要它。缩小列表并更改符号以适应您的语言，然后在需要时尝试添加新元素。此外，如果你需要反复阅读才能理解，也不要难过。当我写这篇文章的时候，我也不得不这么做。坚持下去，你会熬过去的。******

******`*exponential-expression*`:执行取幂运算的表达式。******

*   ******`*postfix-expression*`******
*   ******`*exponential-expression*``******`******

******`*unary-expression*`:包含一元运算符的表达式。******

*   ******`*exponential-expression*`******
*   ******`**++**` `*unary-expression*`******
*   ******`**--**` `*unary-expression*`******
*   ******`*unary-operator*` `*exponential-expression*`******
*   ******`**size**` `**of**` `*unary-expression*`******
*   ******`**size**``**of**``**(**``*type-name*``**)**``**align**``**of**``**(**``*type-name*``**)**`******

******`unary-operator`:只有一个操作数的运算符。`**&**`、`*****`、`**+**`、`**-**`或`**~**`中的一种。******

******`*multiplicative-expression*`:执行乘法运算的表达式。******

*   ******`*unary-expression*`******
*   ******`*multiplicative-expression*``*****`******
*   ******`*multiplicative-expression*``**/**`******
*   ******`*multiplicative-expression*``**%**`******

******`*additive-expression*`:执行加法运算的表达式。******

*   ******`*multiplicative-expression*`******
*   ******`*additive-expression*` `**+**` `*multiplicative-expression*`******
*   ******`*additive-expression*` `**-**` `*multiplicative-expression*`******

******`*shift-expression*`:执行按位移位运算的表达式。******

*   ******`*additive-expression*`******
*   ******`*shift-expression*`*`**<<**`*`*additive-expression*`********
*   ******`*shift-expression*`*`**>>**`*`*additive-expression*`********

******`*bitwise-and-expression*`:执行按位 and 的表达式。******

*   ******`*shift-expression*`******
*   ******`*bitwise-and-expression*`*`**&**`*********

*******`*bitwise-xor-expression*`:执行按位异或运算的表达式。*******

*   ******`*bitwise-and-expression*`******
*   ******`*bitwise-xor-expression*`*`**^**`*`*bitwise-and-expression*`********

******`*bitwise-or-expression*`:执行按位或运算的表达式。******

*   ******`*bitwise-xor-expression*`******
*   ******`*bitwise-or-expression*`*`**|**`*********

*******`*relational-expression*`:执行关系比较的表达式。注意，这些是 Python 风格的关系比较。*******

*   ******`*bitwise-or-expression*`******
*   ******`*relational-expression*` `**<**` `*bitwise-or-expression*`******
*   ******`*relational-expression*`*`**>**`*********
*   *******`*relational-expression*`*`**<=**`*`*bitwise-or-expression*`*********
*   ******`*relational-expression*`*`**>=**`*`*bitwise-or-expression*`********
*   ******`*relational-expression*` `**==**` `*bitwise-or-expression*`******
*   ******`*relational-expression*` `**!=**` `*bitwise-or-expression*`******

******`*logical-not-expression*`:执行逻辑 not 的表达式。******

*   ******`*relational-expression*`******
*   ******`**not**` `*logical-not-expression*`******

******`*logical-and-expression*`:执行逻辑 and 的表达式。******

*   ******`*logical-not-expression*`******
*   ******`*logical-and-expression*` `**and**` `*logical-not-expression*`******

******`*logical-or-expression*`:执行逻辑 or 的表达式。******

*   ******`*logical-and-expression*`******
*   ******【T2`**or**``*logical-and-expression*`******

******`*conditional-expression*`:执行条件运算的表达式。******

*   ******`*logical-or-expression*`******
*   ******`*expression*` `**if**` `*logical-or-expression*` `**else**` `*conditional-expression*`******

******`*assignment-expression*`:执行赋值运算的表达式。******

*   ******`*conditional-expression*`******
*   ******`*unary-expression*` `*assignment-operator*` `*assignment-expression*`******

******`*assignment-operator*`:赋值的运算符。`**=**`、`****=**`、`***=**`、`**/=**`、`**%=**`、`**+=**`、`**-=**`、`**<<=**`、`**>>=**`、`**&=**`、`**^=**`或`**|=**`中的一种。******

******`*expression*`:解析为数值的代码。******

*   ******`*assignment-expression*`******
*   ******`*expression*``**,**`******

******`*constant-expression*`:在编译时具有可计算值的表达式。******

*   ******`*conditional-expression*`******

******![](img/9326abbcceeeb44a62c567a39e6b710a.png)******

******照片由[内森·杜姆劳](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄******

******好吧！恭喜你走到这一步！我们已经设计好了所有的表达方式。你可能会注意到，我主要遵循 C 的语法，但是做了一些遵循 Python 语法的修改。我认为语法的后半部分更容易理解，因为它们都是我们熟悉的运算符。不要惊慌，但是我们只学了 Sea 语法的三分之一。******

# ******声明******

******声明是指定标识符性质的代码。这些允许创建变量、结构、枚举等。请注意，我发现很难用一种有助于理解的方式来标记所有这些，所以我只会在我有需要补充的时候进行标记。******

******`*declaration*`:******

*   ******`*declaration-specifiers*` `*init-declarator-list*`？`**\n**`******
*   ******`*static_assert-declaration*`******

******`*declaration-specifiers*`:******

*   ******`*storage-class-specifier*` `*declaration-specifiers*`******
*   ******`**using**`T11`**as**`T28******
*   ******`*type-specifier*` `*declaration-specifiers*`？******
*   ******`*type-qualifier*` `*declaration-specifiers*`******
*   ******`*function-specifier*` `*declaration-specifiers*`？******
*   ******`*alignment-specifier*` `*declaration-specifiers*`？******
*   ******`**align**` `*declaration-specifiers*` `**as**`******

******`*init-declarator-list*`:******

*   ******`*init-declarator*`******
*   ******`*init-declarator-list*` `**,**` `*init-declarator*`******

******`*init-declarator*`:******

*   ******`*declarator*`******
*   ******`*declarator*` `**=**` `*initializer*`******

******`*storage-class-specifier*`:******

*   ******`**auto**`******
*   ******`**external**`******
*   ******`**register**`******
*   ******`**static**`******

******`*type-specifier*`:******

*   ******`**void**`******
*   ******`**bool**`******
*   ******`**char**`******
*   ******`**short**`******
*   ******`**int**`******
*   ******`**long**`******
*   ******`**float**`******
*   ******`**double**`******
*   ******`**signed**`******
*   ******`**unsigned**`******
*   ******`**complex**`******
*   ******`*atomic-type-specifier*`******
*   ******`*struct-or-union-specifier*`******
*   ******`*enum-specifier*`******
*   ******`*typedef-name*`******

******`*struct-or-union-specifier*`:******

*   ******`*struct-or-union*` `*identifier*`？【T2`*struct-declaration-block*`*`**\n**`*******
*   *******`*struct-or-union*` `*identifier*`*******

******`*struct-or-union*`:******

*   ******`**struct**`******
*   ******`**union**`******

******`*struct-declaration-block*`:******

*   ******`*struct-declaration*`******
*   ******`*struct-declaration-block*`？*`**\n**`**`**\t**`*`indent-level`*`*struct-declaration*`***********

******`*struct-declaration*`:******

*   ******`*specifier-qualifier-list*` `*struct-declarator-list*`？******
*   ******`*static_assert-declaration*`******

******`indent-level`:标签的集合，代表当前代码嵌套的深度。******

******`*struct-qualifier-list*`:******

*   ******`*type-specifier*` `*specifier-qualifier-list*`？******
*   ******`*type-qualifier*` `*specifier-qualifier-list*`？******
*   ******`*alignment-specifier*` `*specifier-qualifier-list*`？******

******`*struct-declarator-list*`:******

*   ******`*struct-declarator*`******
*   ******`*struct-declarator-list*`T32`*struct-declarator*`******

******`*struct-declarator*`:******

*   ******`*declarator*`******
*   ******`declarator`？`:` `constant-expression`******

******`*enum-specifier*`:******

*   ******`**enum**`T5？`**:**` `*enumerator-block*` `**\n**`******
*   ******`**enum**` `*identifier*`******

******`*enumerator-block*`:******

*   ******`enumerator`******
*   ******`*enumerator-block*` *？*`**\n**`**`**\t**`*`indent-level``enumerator`*********

*******`*enumerator*`:*******

*   ******`*enumeration-constant*`******
*   ******`*enumeration-constant*` `**=**` `*constant-expression*`******

******`*atomic-type-specifier*`:******

*   ******`**atomic**` `**(**` `*type-name*` `**)**`******

******`*type-qualifier*`:******

*   ******`**const**`******
*   ******`**restrict**`******
*   ******`**volatile**`******
*   ******`**atomic**`******

******`*function-specifier*`:******

*   ******`**inline**`******
*   ******`**returnless**`******

******`*declarator*`:******

*   ******`*pointer*`？`*direct-declarator*`******

******`*direct-declarator*`:******

*   ******`*identifier*`******
*   ******`**(**``*declarator*`********
*   *******`*direct-declarator*` `**[**` `*type-qualifier-list*`？*T39`*assignment-expression*`？**********
*   ******`*direct-declarator*`*`**[**`**`**static**`**`*type-qualifier-list*`？*`*assignment-expression*`**************
*   *********`*direct-declarator*`*`**[**`*`*type-qualifier-list*``**static**`**`*assignment-expression*`*`**]**`**************
*   *******`*direct-declarator*`*`**[**`*`*type-qualifier-list*`？`*****` `**]**`*********
*   ******`*direct-declarator*`*`**(**`*`*parameter-type-list*`*`**)**`*********

*******`*pointer*`:*******

*   ******`*****` `*type-qualifier-list*`？******
*   ******`*****` `*type-qualifier-list*`？`*pointer*`******

******`*type-qualifier-list*`:******

*   ******`*type-qualifier*`******
*   ******`*type-qualifier-list*`T38******

******`*parameter-type-list*`:******

*   ******`*parameter-list*`******
*   ******`*parameter-list*` `**,**` `**...**`******

******`*parameter-list*`:******

*   ******`*parameter-declaration*`******
*   ******`*parameter-list*`*`**,**`*`*parameter-declaration*`********

******`*parameter-declaration*`:******

*   ******`*declaration-specifiers*` `*declarator*`******
*   ******`*declaration-specifiers*` `*abstract-declarator*`******

******`*type-name*`:******

*   ******`*specifier-qualifier-list*`T48？******

******`*abstract-declarator*`:******

*   ******`*pointer*`******
*   ******`*pointer*`？`*direct-abstract-declarator*`******

******`*direct-abstract-declarator*`:******

*   ******`**(**`T31`**)**`******
*   ******`*direct-abstract-declarator*``**[**`？`*assignment-expression*`？`**]**`******
*   ******`*direct-abstract-declarator*`*`**[**`**`**static**`**`*type-qualifier-list*`？`*assignment-expression*` `**]**`***********
*   ******【T6*`**[**``*type-qualifier-list*`*`**static**`**`*assignment-expression*``**]**`**********
*   ******`*direct-abstract-declarator*` `**[**` `*type-qualifier-list*`？`*****` `**]**`******
*   ******`*direct-abstract-declarator*`*`**(**`*`*parameter-type-list*`？`**)**`********

******`*typedef-name*`:******

*   ******`*identifier*`******

******`*initializer*`:******

*   ******`*assignment-expression*`******
*   ******`**{**` `*initializer-list*` `**}**`******
*   ******`**{**``*initializer-list*``**,**`**********

********`*initializer-list*`:********

*   ********`*designation*`？`*initializer*`********
*   ********`*initializer*` `**,**` `*designation*`？`*initializer*`********

******`*designation*`:******

*   ******`*designator-list*` `**=**`******

******`*designator-list*`:******

*   ******`*designator*`******
*   ******`*designator-list*`T31******

******`*designator*`:******

*   ******`**[**` `*constant-expression*` `**]**`******
*   ******`**.**` `*identifier*`******

******`static_assert-declaration`:******

*   ******`**static**``**assert**``**(**``*constant-expression*``**,**``*string-literal*``**)**`**`**\n**`********

******![](img/69f25a531eeef5e4cc23938188270cea.png)******

******由 [Lukasz Szmigiel](https://unsplash.com/@szmigieldesign?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片******

******唷，我们已经熬过来了！让我们花一点时间来欣赏这幅美丽的图像。******

******好吧。我必须说，对我来说这比表达难多了。我发现它不那么熟悉，也更长。我肯定我犯了拼写错误，(如果你发现了这些，请指出来给我看)而且我肯定我没能正确地理解很多 C 语言的语法。我会尽我所能继续回来，并在发现错误时更新它。现在，我们只剩下陈述和外部定义。幸运的是，这些是最短的部分。******

# ******声明******

******语句是代码执行工作的单位。每行代码通常是一条语句。******

******`*statement*`:******

*   ******`indent-level` `*labeled-statement*`******
*   ******`indent-level`******
*   ******`indent-level` `*expression-statement*`******
*   ******`indent-level` `*selection-statement*`******
*   ******`indent-level`******
*   ******`indent-level`******
*   ******`indent-level`******

******`*jump-statement*`:******

*   ******`*goto-statement*`******
*   ******`*continue-or-break-statement*`******
*   ******`*return-statement*`******

******`*goto-statement*`:******

*   ******`**go**`**`**to**``*identifier*`********
*   ******`**go**`**`**to**``*identifier*`*`**if**``*expression*``**\n**`*********

******`*continue-or-break-statement*`:******

*   ******`*continue-or-break*` `**\n**`******
*   ******`*continue-or-break*`T33`*expression*`******

******`*continue-or-break*`:******

*   ******`**continue**`******
*   ******`**break**`******

******`*return-statement*`:******

*   ******`**return**``*expression*`******
*   ******`**return**``*expression*``**if**``*expression*`******

******`*compound-statement*`:******

*   ******`**\n**`？`*declaration-list*`？`*statement-list*`？******

******`*declaration-list*`:******

*   ******`*declaration*`******
*   ******`*declaration-list*` `*declaration*`******

******`*statement-list*`:******

*   ******`*statement*`******
*   ******`*statement-list*` `*statement*`******

******`*expression-statement*`:******

*   ******`*expression*` `**\n**`******

******`*iteration-statement*`:******

*   ******`**while**` `*expression*` `**:**` `*statement*` `*else-clause*`？******
*   ******`**do**``:``*statement*``**while**``*expression*`*`**\n**`？`*else-clause*`？*******
*   ******`**for**` `*expression*`？`**;**` `*expression*`？`**;**`T5？`**:**``*statement*`*`*else-clause*`？*******
*   ******`**for**``**each**``*declaration*``**in**``*expression*``**:**``*statement*``*else-clause*`******

******`*else-clause*`:******

*   ******`**else**`******

******`*selection-statement*`:******

*   ******`**if**` `*expression*` `**:**` `*statement*` `*else-clause*`？******
*   ******`**falling**`？`**switch**` `*expression*` `**:**` `*statement*`******

******`*labeled-statement*`:******

*   ******`*identifier*``**:**`******
*   ******`**case**`T36`**:**`******
*   ******`**default**``**:**`******

# ******外部定义******

******`*translation-unit*`:******

*   ******`*external-declaration*`******
*   ******`*translation-unit*` `*external-declaration*`******

******`*external-declartion*`:仅在外部(文件)范围内允许******

*   ******`*function-definition*`******
*   ******`*declaration*`******

******`*function-definition*`:声明符这里是函数声明符******

*   ******`*declaration-specifiers*`？`*declarator*`T17？`*compount-statement*`******

******我告诉过你没剩多少了！让我简单地说一下，我已经为这篇文章工作了几天，所以如果它不是那么容易理解的话，请原谅我。我很乐意接受你的改进建议。如果你能指出我犯的错误，我会特别高兴。好消息是，现在我已经为 Sea 创建了语法，我可以开始写下一篇文章了，在那里我们开始编程！随着我编写越来越多的 Sea 程序，我可以找到我在语法上犯错误的地方。特别是，我对缩进和换行符的要求并不严格。不管怎样，这已经够长了，让我在这里结束。******

******![](img/fc0b880d6dbcf8c1c23284cf66ce0cf2.png)******

******伊恩·斯托弗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片******

# ******收尾工作******

******如果您正在创建自己的编程语言，从语法开始可能是一项艰巨的任务。这是创造一门语言最难的部分之一；然而，这就是我建议从它开始的原因。查看你知道的语言，搜索它们的语法。这里以 [Python 的官方语法](https://docs.python.org/3/reference/grammar.html)为例。你不是在创造世界上第一种编程语言；您正在创建一个基于所有现有编程语言的变体。所以，用那些语言吧！人们已经花了几十年的时间来优化这些语言，所以从这里开始，做你想要的修改。例如，除了 C 语法的一般 than 化之外，我还为 Sea 添加了一些东西:******

*   ******For-each 循环。与使用传统的 for 循环相比，这将允许在不使用更多资源的情况下轻松迭代列表。******
*   ******`**falling**` 关键字用于切换报表。这个修饰符将使 switch 语句像它们传统的行为一样；如果不中断，执行将通过 switch 语句失败。我发现大多数时候，失败行为并没有那么有用。因此，默认情况下，switch 语句将表现为好像每个 case 都有一个断点。不过可以用`**continue**` 来落井下石。******
*   ******条件转到、中断、继续、返回。通常，只有当条件为真时，才需要跳转。因此，我提供了一个可读的单行解决方案。我个人不喜欢 Python 中单行 if 语句的外观，所以我创建了这个语法。它不会与条件语句混淆，因为它永远不会有 else 子句，而条件语句总是有。******

******我还将向您介绍 Python 中循环的 else 子句(如果您没有从循环中断开，将执行 else 语句)以及 c 中的 do-while 循环。如果这意味着在不同的情况下创建更多的可读性，我更希望 Sea 具有冗余符号。我还清理了 C 语言中一些奇怪的下划线过多的关键字，并将多字关键字拆分成两个关键字。这使得 Sea 读起来稍微更像一种自然语言，因此是最佳的，即使这意味着可能的标识符稍微少一些。******

******期待下一篇文章，在那里我将开始用 Python 编写 Sea，我们可以开始看到所有这些语法是如何工作的。如果你现在没有完全理解语法结构，也没关系；一旦我们开始写代码，它会变得更加清晰。查阅我的[编程语言列表](https://davidjmacdonald.medium.com/list/how-to-create-a-programming-language-0ee2bcc7bccd)所涉及的前后文章！******