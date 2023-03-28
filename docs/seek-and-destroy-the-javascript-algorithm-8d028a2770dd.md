# 寻找和破坏 JavaScript 算法

> 原文：<https://medium.com/codex/seek-and-destroy-the-javascript-algorithm-8d028a2770dd?source=collection_archive---------1----------------------->

![](img/289fd610ad6f290646d48ddf1ccea317.png)

图片来自 Loudwire

在我们的算法练习过程中，有些练习很无聊，有些会很有趣，还有一些你会忍不住去写博客。我前几天碰到的这个好玩的算法就是这样。寻找和摧毁，让我听到詹姆斯·海特菲尔德唱出来的算法加载*搜索，寻找和摧毁*与他的可识别的声音。是的，我说的是 Metallica 和一种算法，该算法将搜索初始数组中与该数组后面的参数相同的值，并销毁它们。

比如`seekAndDestroy([1, 2, 3, 5, 1, 2, 3], 2, 3)`应该返回`[1, 5, 1]`。

因此，让我们用 Seek 和 Destroy 来引爆我们的耳机，并查看一些解决方案。

# 嵌套循环

对于第一个解决方案，我们使用`Object.values(arguments).slice(1)`创建一个数组，并将其存储在变量`valuesToRemove`中，以便与 arr 进行比较。我们开始一个基本的 for 循环来遍历`arr`。然后，在第一个循环中嵌套另一个 for 循环，将整数变量`j`和`arr`改为`valuesToRemove`。第二个循环将遍历`valuesToRemove`。在第二个循环中创建一个 if 语句，严格检查 arr[i]的当前值是否等于`valuesToRemove[j]`。如果当前索引*处的值在两个数组中*相等，我们将把它从`arr`中删除。在嵌套循环之外:返回修改后的数组，过滤掉由`delete`操作符创建的任何空值。

您可能认为这段代码太复杂了，这是正确的。当我们通过输入添加嵌套迭代时，会增加复杂性。根据[大 O 符号](https://dzone.com/articles/learning-big-o-notation-with-on-complexity)，这是一个 *O(n2)* 函数，它代表一个复杂度与输入大小的平方成正比的函数。因此增加了复杂性。换句话说，反应会更慢，需要更多的内存，这比圣安格尔的小军鼓声音更糟糕。相当糟糕。

![](img/a5b6e21b15255549480eabd65496bee8.png)

拉尔斯看起来像是在吹嘘自己站起来打鼓的能力。声音较大的图片。

# 阵列迭代方法

我们的第二个解决方案是最常见的 JavaScript 解决方案，本练习中的*恩特尔·桑德曼*。它用`.filter`和`.includes`方法迭代新数组。正如我们所看到的，这是一个更干净的代码。[迭代方法](https://dev.to/lukocastillo/time-complexity-big-0-for-javascript-array-methods-and-examples-mlg)如`.filter`的时间复杂度为 *O(n)* ，与输入次数成正比线性增加。起初，迭代方法看起来令人困惑。一旦我们理解了如何使用它们，这些方法，在需要的时候实现它们，将会像在卡拉 ok 之夜跟着*恩特尔·桑德曼*唱歌一样自然。

![](img/4fd6858ab180b66f6e51222adf9e41ca.png)

Retsuko 对恩特尔·桑德曼反应过度了。图片来自 BBC。

# 休息参数

第三个也是最后一个例子是我最喜欢的。这是克里夫伯顿(瑞普)玩低音独奏史诗级。在这个解决方案中，我们使用了 [rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)语法。我们允许一个函数使用三个点`…`作为数组接受不确定数量的参数。然后，我们用`.includes`方法返回过滤后的数组。这个解决方案是一些漂亮的 ES6 代码。

听听那个电贝司。他是有史以来最好的贝斯手之一。

所以我们有了它，一个有趣的算法，我们可以用金属乐队的一首歌来命名。我希望你能从这篇文章中学到一些新的东西，并像我喜欢写它一样喜欢它。所以，继续研究这些算法，找到一些你最喜欢的音乐或流行文化来参考它。

编码快乐！

# 总结:

1.  “查找并销毁”算法简介。
2.  嵌套循环
3.  阵列迭代方法
4.  Rest 参数语法

# 参考资料:

1.  [学习 O(n)复杂度的大 O 记数法](https://dzone.com/articles/learning-big-o-notation-with-on-complexity)牛顿，丹。
2.  [时间复杂度大 0 的 Javascript 数组方法和例子](https://dev.to/lukocastillo/time-complexity-big-0-for-javascript-array-methods-and-examples-mlg)作者 Castillo，Luis。
3.  MDN Web Docs，[其余参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)