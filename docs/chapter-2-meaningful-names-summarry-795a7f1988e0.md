# 干净代码—有意义的名称(摘要)

> 原文：<https://medium.com/codex/chapter-2-meaningful-names-summarry-795a7f1988e0?source=collection_archive---------11----------------------->

![](img/5530c0cb1c78f2ead365c23650b6df23.png)

src:干净代码第 2 章

在编程的世界里，我们从未脱离命名，无论是命名变量、函数、参数、类还是包。然而，我们经常发现没有意义的名字，这使得维护或与其他程序员合作变得更加困难。所以，要避免这些事情，我们最好还是做好。以下是一些如何起好名字的简单规则。

# 使用透露意图的名字

名字应该揭示意图。所以我们知道如何以及何时再次使用它们。

> “选择好名字需要时间，但节省的时间比花费的多”

如果你命名了一个变量、函数或类，确保它回答了所有的大问题。选择能够解释其存在原因、作用和用途的名称

```
int d; // elapsed time in days
```

相对

```
int elapsedTimeInDays;
```

这个名字并没有揭示什么，它既没有唤起人们对时间流逝的感觉，也没有唤起人们对日子的感觉。最好使用`elapsedTimeInDays`，因为它揭示了意图，也更容易理解。

# 避免虚假信息

程序员必须避免留下模糊代码含义的错误线索。例如，如果你正在编写一个斜边，也许你认为`hp`是一个很好的缩写。但是在现实中，其他程序员可能有不同的解释(例如手机)。

不要把一组账户称为`accountList`，除非它实际上是一个列表，因为列表这个词对程序员来说有特定的含义。

注意不要使用差别不大的名字。

```
XYZControllerForEfficientHandlingOfStrings
```

和

```
XYZControllerForEfficientStorageOfStrings
```

这可能会导致错误，因为单词的形状非常相似，程序员可能很难注意到这一点

# 进行有意义的区分

有时，当两个事物相似时，你可以加上数列(`a1, a2, …aN`)或干扰词。但这是不够的，如果名字必须不同，那么他们也应该意味着不同的东西。

```
public static void copyChars(char a1[], char a2[]) {
for (int i = 0; i < a1.length; i++) {
a2[i] = a1[i];
}
}
```

相对

```
public static void copyChars(char source[], char destination[]) {
for (int i = 0; i < a1.length; i++) {
destination[i] = source[i];
}
}
```

使用像`a1`或`a2`这样的名字会花费更多的时间。我们最好使用`source`和`destination`来命名参数。

噪音词也是无意义的相赠，想象你有一个`Product` 类。如果你有另一个叫做`ProductInfo`或`ProductData`的，你只是让它们的名字不同，而没有让它们有任何不同的意思。干扰词是多余的，例如，你不应该用单词`table` *来命名一个表。*给一个变量命名，你不应该用`nameString` *，*一个名字会是浮点数字吗？

# 使用易发音的名字

确保你选择的名字是可以发音的，因为如果你不能发音，如果你想和某人讨论，那就很难了，然后你会讨论它，听起来像个白痴

> "嗯，在这里，我们有一个小房间，看见了吗？"

比较:

```
class DtaRcrd102 {
private Date genymdhms;
private Date modymdhms;
private final String pszqint = “102”;
/* … */
};
```

到

```
class Customer {
private Date generationTimestamp;
private Date modificationTimestamp;;
private final String recordId = “102”;
/* … */
};
```

试想一下，如果要讨论和念第一个！

# 使用可搜索的名称

使用单个字母的名字很难在正文中找到。例如，名称`e` 对于程序员可能需要搜索的任何变量来说都不是一个好的选择。这是英语中最常见的字母，当程序员试图搜索这个字母时会很困难。作者建议单个字母的名字只能在短方法中作为局部变量使用。*名称的长度应该与其作用域的大小相对应*。如果一个变量或常量可能会在一段代码中的多个地方出现或使用，就必须给它一个便于搜索的名称。比较

```
for (int j=0; j < 34; j++) {
s += (t[j]*4)/5;
}
```

到

```
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j=0; j < NUMBER_OF_TASKS; j++) {
int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
int realTaskWeeks = (realdays / WORK_DAYS_PER_WEEK);
sum += realTaskWeeks;
}
```

找到`WORK_DAYS_PER_WEEK`比找到所有使用过 5 的地方要容易得多。

# 避免编码

编码的名字很难发音，很容易打错。避免使用匈牙利符号或成员前缀。现在，ide 足够智能来识别类的成员变量类型并给出建议。所以我们不需要它们。不要用一堆信息来扰乱你的大脑。

# 避免心理映射

程序员是聪明人。但是不要在代码里炫耀自己的聪明。显然，你可以记住`r`是 URL 变量的简称，但是其他程序员可能不知道这种心理映射。

> 聪明的程序员和专业的程序员的一个区别是，专业的程序员明白清晰才是王道。专业人士善用他们的能力，编写他人可以理解的代码。

# 类名

类和对象应该有像`Vehicle`、`Food`这样的名词或名词短语名称。避免使用动词作为类名。

# 方法名称

方法应该有动词或动词短语名`likepostPayment`、`deletePage`或`save`。避免使用动词作为类名。

# 别装可爱了

一个可爱的名字只会让那些和作者一样有幽默感的人记住，而且只要这些人记得这个笑话。代码中的可爱往往以俚语的形式出现。比如不要用`whack()`这个名字来表示`kill()`。说你的意思。说话算数。

# 为每个概念选择一个词

避免在同一个项目中使用同音词。例如，不要使用`fetch`、`retrieve`和`get`。它可以如此令人困惑。

# 不要一语双关

避免将同一个词用于两个目的。比如一个`add`的方法。如果要通过添加或连接两个现有值来创建新值。会没事的。但是如果你想把一个单一的值放入一个集合，那就一语双关了。在这种情况下，最好使用`insert`或`append`

# 使用解决方案域名

作为一名程序员，你通常会在团队中工作。意味着你的伴侣也是程序员。所以继续使用计算机科学(CS)术语、算法名称、模式名称、数学术语等等。从问题域中抽取每个名称是不明智的，因为我们不希望我们的同事跑来跑去地询问客户每个名称的含义，而他们已经知道不同名称的概念。例如，名称`JobQueue`对于熟悉队列模式的程序员来说意义重大。

# 添加有意义的上下文

为了获得有意义的上下文，您需要通过将名称封装在命名良好的类、函数或名称空间中，将名称放在读者的上下文中。比如`firstName`、`lastName`、`street`、`houseNumber`、`city`、`state`、`zipcode`。最好将它们包含在一个名为`Address`的类中。当所有其他内容都无法包含时，作为最后的手段，可能有必要在名称前加上前缀。

# 不要添加不必要的上下文

名称`accountAddress`和`customerAddress`是类`Address`的实例的精确名称，但可能是类的糟糕名称。`Address`是一个*类*的别名。如果你需要区分 MAC 地址、端口地址和 Web 地址，你可以考虑`PostalAddress`、`MAC`和`URI`。由此产生的名字更加精确，这是所有命名的要点。

> 遵循这些规则，看看你是否没有提高代码的可读性。如果您正在维护其他人的代码，请使用重构工具来帮助解决这些问题。短期内会有回报，长期来看会继续有回报。”

这些都是我一直在努力学习的那本书的第二章总结，并且得到了书名为《干净的代码》的那本书的要点，作者是 Robert C. Martin。

请随时提供一些建议和反馈。谢了。

# 参考

罗伯特·c·马丁的《干净的代码》