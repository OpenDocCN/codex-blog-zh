# 4 个日期和时间操作库

> 原文：<https://medium.com/codex/4-date-and-time-manipulation-libraries-470b84d24be7?source=collection_archive---------10----------------------->

## [抄本](http://medium.com/codex)

## *再也不用纠结于 JavaScript 日期格式了。*

![](img/4377611e8df494b22c1bcf259200b11e.png)

泰勒·哈里斯在 [Unsplash](https://unsplash.com/s/photos/rolex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

在 JavaScript 中处理日期对象可能是一项艰巨的任务。尤其是当您使用多个时区或多种日期格式时。美国的格式不同于欧洲的格式。一些人或一些编程语言或数据库使用另一种格式。

这些库将帮助您正确、轻松地解析、格式化和使用这些格式。这些库是众所周知的，并且运行良好。享受这份名单。

# 日期-FNS

在 JavaScript 中操作日期对象的一个不太为人所知的库是[日期 FNS](https://date-fns.org/) 。易于学习和维护的功能丰富的库。它使用 JavaScript 中的`Date`对象，并提供加减日期或获取两个日期之间的天数的功能。

它可以安装在一些方式，如 NPM 和它的作品很好地与捆绑，如网络包。它支持 TypeScript 和 Flow，文档也很丰富。

# 瞬间

最流行的操纵日期和时间的库之一是 [Moment](https://momentjs.com/) 。瞬间很不幸的慢慢得到[弃用](https://momentjs.com/docs/#/-project-status/)。然而，对于学习程序员和项目类型的小子集中的小项目来说，它仍然是一个很好的选择。

这个库有很多特性，从添加或删除分钟或小时，到解析和格式化你可能需要的每种格式。它很容易使用，也使用了`Date`对象。许多项目仍然在使用这个库，所以在这个列表中值得注意。

# 国际光子

新的时刻， [Luxon](https://moment.github.io/luxon/) ，使用`Intl`物体代替`Date`物体。这使得它更经得起未来的考验，是新项目的最佳选择。语法非常相似，如果你以前使用过 Moment，学习曲线很短。

它拥有 Moment 的所有优点和特性，只是使用了稍微不同的语法和对象。它的特点是支持时区，并且慢慢地被越来越多的人采用。

# DayJS

DayJS 是 Moment 的一个非常小的替代品。它只有 2kb 大小，并且使用相同的现代 API。它与浏览器中的节点和 JavaScript 一起工作。它有很多特点，很容易学习。它非常类似于 Moment，当大小是一个问题时，它的用例将有助于脱颖而出。它使用了`Date`对象。

# 结论

日期操作变得简单了。如果你使用这些库，日期和时间操作变得容易而不是困难和烦人。然而，你可能需要研究一下`Date`和`Intl`对象的不同来做出选择。

感谢您的阅读，祝您度过美好的一天。