# 一堂简单的编程课:操场

> 原文：<https://medium.com/codex/a-simple-lesson-in-coding-the-playground-3ef0326b6506?source=collection_archive---------27----------------------->

![](img/82705d4d3bc94230ec05c92899940e64.png)

密苏里州菲利普斯堡糖果厂的泡菜味太妃糖。(作者照片，2021)。

这是四篇系列文章中的最后一篇，讲述了一个简单的想法是如何演变成学生第一堂数据结构课的课程计划的。 [第一篇文章](/@leoirakliotis/a-simple-lesson-in-coding-38334e9d8608)描述了我们将在这里继续使用的简单例子:一种判断字符串是否是回文的方法。[第二篇](/@leoirakliotis/a-simple-lesson-in-coding-revisited-fbc9add1e599)稍微提炼了一下方法。第三篇文章[暴露了更多的缺陷，并为编码前更好的规划和设计提供了一个案例。](/@leoirakliotis/a-simple-lesson-in-coding-testing-first-91b3da7b9925)

既然我们对我们的方法的工作方式感到满意，是时候在真实环境中部署它了:让我们扫描一本书并收集它的回文。按照下面的计划，我们将使用古登堡计划作为数据来源。

```
establish a URL connection to the book
scan the text, word by word
if a word has not been processed before:
  if it's a valid word (letters, spaces, punctuation only):
    if it's a palindrome:
      add it to the list of palindromes for the book
report results
```

这个计划看起来很简单，除了关于“回文列表”的部分。学完编程入门课程的学生对数组很熟悉。自然，他们可能倾向于编写如下代码。在下面的代码片段中，`book`是一个连接到基于 web 的文本的`Scanner`对象，`palindromesFound`是一个`String`数组。

上面代码的问题当然是数组`palindromesFound[]`。用太少的元素初始化它，我们会得到一个索引越界运行时错误。用太多的元素初始化它，我们可能会浪费资源。理想情况下，我们需要一个按需调整大小的数组。Java 中的数组不能做到这一点。所以学生们现在必须“发明”动态数组。

有些程序员可能会问，为什么不在这一点上向学生展示如何使用数组列表呢？我可以，但是我的目标是向学生展示数据结构是特定问题的解决方案，并且数据结构的机制完全在他们的编程能力之内。最终，我们将使用 Java 集合框架和 Guava 中的数组列表和其他类。目前，重点是理解必要性如何变成效用。简单的编程就能让我们达到目的。

记住这一点，邀请学生解决调整数组大小的问题。如果我们有一个为[10]个元素初始化的数组，但是我们想再存储一个元素，该怎么办？经过课堂上的反复讨论，我们发现我们可以在 Java 中调整数组的大小，尽管效率不是很高:

现在，我们关注调整数组大小的频率。如果我们为每一个额外的元素调整大小，事情会变得非常慢。我们试验不同的调整大小计划，重写第 1 行，如下所示，同时我们讨论不同调整大小因子的优点。

```
String newArray[] = new String[oldArray.length * 2]; 
// or
String newArray[] = new String[3 * oldArray.length / 2];
```

最后，我们派生了一个类来处理动态调整大小。本质上，我们正在重新发明数组列表。

注意方法 *resize* 是私有的。这是因为调整数组大小是类的内部事务，不应该由用户发起。每当我们达到数组的容量时，方法 add 就会调用方法 resize。

通过使用 FlexArray 对象，我们认识到一本书，例如，[詹姆斯·乔伊斯的*尤利西斯*](https://www.gutenberg.org/ebooks/4300) *，*有数千个回文。经过仔细检查，我们发现大多数都是重复的。每当我们的代码遇到不定冠词*“a”*时，它会将它添加到数组`palindromesFound`中。结果，我们最终在那个数组中有数千个元素，包含相同的值(例如，`"a"`，不定冠词；`"i"`，第一人称代词；`"did"`，助动词 *do-* 的屈折形式；等等)。

因此，下一个目标是避免处理重复的单词，确保数组`palindromesFound`不包含重复的单词。为此，我们需要一个单独的`FlexArray`对象来包含已经处理过的单词。

```
FlexArray wordsProcessed = new FlexArray();
FlexArray palindromesFound = new FlexArray();while (book.hasNext()) {
  String word = book.next();
  if (!wordsProcessed.contains(word) { // first time word
    wordsProcessed.add(word); // remember this word
    if (isValid(word) && isPalindrome(word)) { // if palindrome
      palindromesFound.add(word); // add it to our collection
    }
  }
}
```

使用这种方法，我们发现像*尤利西斯*这样的巨著，回文单词很少；就几百块。这是因为 FlexArray `wordsProcessed`“记住”我们之前学过的单词，并帮助我们跳过它们。当我们在 FlexArray `palindromesFound`中添加一个单词时，我们确定我们没有复制一个现有的条目。事实上，*尤利西斯*有 25 万多字，但只有 4.9 万左右是独一无二的。书中大约有 400 个回文。

使用类`FlexArray`搜索回文会很慢。这是因为方法`contains`从头开始扫描数组，直到找到它要寻找的单词。这不是一个非常有效的搜索单词的方法。在这一点上，我开始向学生描述我们过去如何在印刷字典中搜索单词。这个描述引出了在*二叉查找树中添加和搜索数据的方法。*

虽然并不完美，但我在这里和以前的文章中描述的课程计划帮助学生从他们的入门编程课程过渡到他们的第一个数据结构。