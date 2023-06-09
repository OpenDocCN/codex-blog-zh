# 遗留代码

> 原文：<https://medium.com/codex/legacy-code-2cb94a33dcd0?source=collection_archive---------11----------------------->

![](img/ad677248b14f3ebd41891ba7be329124.png)

摘自维基百科[的打孔卡 https://en . Wikipedia . org/wiki/Punched _ Card #/media/File:Used _ punch Card _(5151286161)。jpg](https://en.wikipedia.org/wiki/Punched_card#/media/File:Used_Punchcard_(5151286161).jpg)

如果你在软件工程领域工作过一段时间，你会抱怨公司的遗留代码。很难理解，很难维护，也很难改变。我当然有。当然，这不是我们的功劳。抱怨遗留代码是可悲和忘恩负义的。我尽力做到两者都不是，并取得了不同程度的成功。

毕竟，什么是遗留代码？这是有效的代码，而且是真正有效的代码。并不是说它已经通过了你的单元测试，或者遵循了你的风格指南，或者有一个现代的和一致的架构。我指的是更基本的“工作”:它让你的公司赚钱。这应该是显而易见的，如果运行它实际上花费你的钱，你会摆脱它。但是我们从来没有想要摆脱遗留代码，我们只是想要重写它。这证明它正在执行一些重要的功能。不像你上周合并到开发分支的代码，遗留代码是给你发工资的。想想吧。遗留代码是为你支付抵押贷款创造必要收入的软件，尽管你嘲笑它。它支持你，你却对它嗤之以鼻。

更糟糕的是，你可能会对你的老板撒谎。亲爱的读者，你曾经在讨论技术债务的时候提到过遗留代码吗？有可能你有，我相信你同意这在我们的行业中经常发生。因此，我们已经确定遗留代码可以产生金钱。相反，债务不会。这就是它成为一种责任的原因。让公司赚钱的东西不是负债，是资产。你上周写的代码，还在等下一个发布？这也是一种责任。毕竟，它花了公司很多钱来生产(软件工程师并不便宜)，而且还没有给公司赚到钱。希望它能很快成为一项资产。我想会的，我对你有最大的信心。

关键是，当我们看遗留代码时，我们看到的是成功。你花了多少时间去寻找成功的代码是什么样的？也许你看过会议演讲，参加过会议，或者买过关于它的书。也许你甚至读过那些书。但是你们公司有成功的例子。真实的例子。我们的行业太急于驳回的例子。

到目前为止，我只把遗留代码看作是为公司赚钱的代码。基于这个事实，我得出结论，这段代码是好的。我确实打算更进一步。我想说遗留代码应该通知新的开发。

让我们先做一些假设。为什么遗留代码是这样写的？很简单，它是由一个小型团队快速编写的，他们无法使用我们今天拥有的工具。我们之所以知道这一点，是因为在编写这段代码的早期，公司已经发展壮大，而当时公司还比较穷，所以快速行动是必要的。公司需要收入，重构根本不是讨论的话题。当然，这是一种概括，但这种概括应该让你停下来。比方说，你在一家书店里，有两本书并排放着:

> 干净的代码:敏捷软件工艺手册
> 
> 好代码:用更小的团队更快地交付产品。用虚构的名字

当然，第二本书至少比第一本书更有吸引力。但是，第二本书最吸引人的部分并没有反映在书名中。这本书是专门针对你的软件领域的。它并没有试图教你如何通过模拟一个假的网上精品巧克力店来制作一个企业计费系统。它用一个企业计费系统的例子教你做一个企业计费系统；一个工作的，有客户的。FooBar Sweet Shop 到底卖出了多少巧克力棒？

这本书(遗产代码，如果你没有理解这个隐喻的话)也向你展示了一个无 bug 代码的例子。我理解你的反对。也许你偶然发现了这篇文章，因为你正试图修复遗留代码中的一个 bug。遗留代码没有大的错误，我可以通过指出它已经被成功使用了很多年来证明这一点。当然，它可能有一些小错误，但是没有什么可以阻止它被部署和使用。没有什么能阻止它为公司赚钱。这样，它就是一个平衡交付和产品质量的例子。记住，一个月前发布的足够好的代码是一种资产。六个月内发布的完美代码是一种负担(如果你真的能在截止日期前完成)。

是的，遗留代码有问题。这就是为什么我们给它起了个特别的名字。它可能很难理解，也很难改变或扩展。这篇文章的重点并不是要否定整块布。要点是我们需要将遗留代码视为我们前进时随身携带的资产，而不仅仅是一个需要克服的问题。

脚注

1.  这种将未发布的代码视为负债的解释摘自《艾利·M·高德拉特和杰夫·考克斯的目标》一书那本书是关于制造业的，并将库存视为一种负债。如果我见过的话，未完成/未部署的代码就是库存。
2.  选择这个例子是因为它是一本著名的书。别想太多。