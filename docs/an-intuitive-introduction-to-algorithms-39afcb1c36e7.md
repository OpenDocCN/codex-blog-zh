# 算法的直观介绍

> 原文：<https://medium.com/codex/an-intuitive-introduction-to-algorithms-39afcb1c36e7?source=collection_archive---------10----------------------->

![](img/046c5d10f6e6e6f0b11a94334e56b9b0.png)

图片来自穆罕默德·哈桑 Pixabay

# 什么是算法？

作为一名程序员，拥有大量可供自己使用的工具是很重要的。这可能包括编程语言、文本编辑器、软件包等。也许最重要的是，对现有的各种类型的算法有一个坚定的理解是至关重要的。这些将成为技术面试和工作场所中最重要的工具。

对于那些不知道的人来说，算法并不是一个很难理解的概念。算法是简单的定义良好的，逐步解决问题的方法。例如，在许多计算机科学课程中有一个练习，要求学生解释如何制作花生酱和果冻三明治。令人震惊的是，这样做的过程是一个算法。麻省理工学院夏令营的讲义提供了关于这个算法的见解:

```
1\. Pick up a slice of bread 
2\. Open the jar of peanut butter by twisting the lid counter clockwise 
3\. Pick up a knife by the handle 
4\. Insert the knife into the jar of peanut butter 
5\. Withdraw the knife from the jar of peanut butter and run it across the slice of bread 
6\. Take a second slice of bread 
7\. Repeat steps 2-5 with the second slice of bread and the jar of jelly.
8\. Press the two slices of bread together such that the peanut butter and jelly meet
```

如你所见，每一步都非常清晰，没有出错的余地。这是因为算法通常是由计算机解释的，而计算机在客观上是哑的。例如，假设你给计算机一个 7 件事情的清单，并要求第 8 项。一个聪明的人只会回答:“单子上只有七件事，所以我不能给你第八件。”然而，电脑可能会坏掉。

从好的方面来看，计算机速度很快，这使得它们能够比人类更高效地运行真正复杂的算法。例如，一台计算机可以在几分之一秒内对一个包含 100 件物品的列表进行排序，只要它收到详细说明如何做的明确指令。

# 存在哪些类型的算法？

人们要求计算机完成各种各样的任务，并且要求计算机快速完成这些任务。按照每个联系人的姓氏对联系人列表进行排序，在 YouTube 上搜索特定的视频，这样的例子不胜枚举。自从第一台计算机发明之前，学者们就在研究算法:设计新颖、有效的方法来解决问题。我将介绍两个比较著名的算法范例:

*   **蛮力方法**

假设你正在字典中寻找“谜”这个词。聪明的人会简单地翻到包含字母 e 的部分，然后从那里开始。很有效率，对吧？不幸的是，计算机不太懂英语。因此，有人可能会建议使用蛮力方法来解决这个问题。

**蛮力方法**需要查看每页上的每个单词，直到出现单词 enigma。这是一个相当低效的任务。事实上，我们可以说，如果在单词 enigma 之前的字典中有 *n* 个单词，我们将必须在找到正确的单词之前执行 *n* 个操作(在这种情况下，一个操作是检查我们是否到达了单词 enigma)。

让我们想象一下最坏的情况。假设您想要搜索单词“zzzzzz”显然，这个词并不存在，但是如果你想仔细检查呢？使用蛮力方法，这将需要您翻阅每一页，查看每个单词，直到您找到字典中的最后一个单词(Zyzzyva ),以便确定“zzzzzz”不是一个单词。类似于 enigma 的例子，如果我们假设字典中有 *n* 个单词，这将需要 *n* 次运算。一定有更有效的方法来解决这个问题。

*   **各个击破**

现在让我们假设你正在看的字典是有序的，大多数都是如此。这意味着你可以把字典分成几个部分，并按字母顺序确定每个部分中的单词是出现在“zzzzzz”之前还是之后。

工作流程如下所示:

```
1\. Find the center of the dictionary2\. Determine if "zzzzzz" is at the center. If so, our job here is done3\. If not, determine if "zzzzzz" comes before or after the center word in the dictionary alphabetically4\. If "zzzzzz" comes before the center, repeat these steps with the first half of the dictionary. Otherwise, repeat these steps with the second half of the dictionary
.
.
.
```

最终，你会多次拆分字典，以至于只剩下一两个单词。在这两种情况下，确定剩余单词是否是“zzzzzz”的过程都非常简单。与强力方法不同，您可以跳过词典的大部分内容而达到这一点。

将工作负载分成越来越小的部分的方法被称为**分而治之。**这种算法范式利用了所谓的*递归，*在递归中，问题被定义为其自身的一个更简单的版本。在这种情况下，问题是在整个字典中搜索“zzzzzz”。“更简单的版本”是在字典的一小部分中搜索“zzzzzz”。

这种解决方案的效率究竟有多高？为了确定这一点，我们需要计算我们执行了多少操作。为简单起见，我们将上述工作流中的步骤 1 到 4 算作一个操作。我们可以这样做，因为在每一步中执行的琐碎操作的数量不会导致低效率:是所采取的步骤的数量使代码运行缓慢。

所以在每一步，我们执行一个操作。我们走了多少步？在每一步中，我们要么找到“zzzzzz ”,要么把字典的大小分成两半。因此，如果整个词典包含 *n* 个单词，则在每个步骤中被忽略的单词量为:

```
n/2 -> n/4 -> n/8 -> n/16 -> n/32 -> .... -> 1
```

通过一点数学操作，我们最终认识到，与在最坏的情况下需要 *n* 次运算相反，我们现在只需要 *log n.* 因此，如果字典包含 100 万个单词，暴力方法在最坏的情况下需要 100 万次运算，而分治法只需要大约 20 次。还不错！

*注意，在这种情况下，对数是以 2 为底的对数。然而，类似于在每个步骤执行的操作量可以被忽略，这也是可以的。关于原因的解释，请随意查看此链接:*[*https://cs . stack exchange . com/questions/78083/why-is-the-log-in-the-big-o-of-binary-search-not-base-2*](https://cs.stackexchange.com/questions/78083/why-is-the-log-in-the-big-o-of-binary-search-not-base-2)

# 为什么我们只关心最坏的情况？

在谈论了这么多最坏的情况后，计算机科学家可能听起来像一群慢性悲观主义者。然而，这与事实相去甚远。

我们担心最坏的情况，因为我们希望最小化最坏情况的负面影响(或经历最坏情况的可能性)。负面影响可能包括过度的内存消耗、长运行时间或潜在的无限延迟。

有一个特定的符号与分析最坏情况运行时联系在一起。它被称为大 O 符号。在阅读本文时，您已经知道了两种主要搜索算法的巨大效率:

*   我概述的强力搜索策略的技术名称是顺序搜索。回想一下，在最坏的情况下，该算法将需要 *n* 个步骤/操作。因此，这个算法的大 O 效率被写成 O( *n* )
*   分而治之策略的技术名称是二分搜索法。由于它在最坏的情况下需要进行 *log n* 操作，所以它的大 O 效率是 O( *log n*

# 结论

现在你知道了！你现在知道了两种非常低效的查字典的方法。你现在也知道如何衡量和描述他们的效率。然而，我真诚地希望你能决定谷歌一下单词的定义。

感谢您花时间阅读本文。请让我知道你是否觉得这很有帮助，或者你是否对我将来应该涉及的计算机科学主题有任何建议。黑客快乐！