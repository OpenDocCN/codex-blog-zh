# 优化开发冷启动

> 原文：<https://medium.com/codex/development-cold-start-dad87e7836d1?source=collection_archive---------0----------------------->

![](img/e92aca87cbb5343764a43dc19e84db04.png)

尤利娅·马特维延科拍摄的照片

在我作为一名软件工程师在亚马逊的职业生涯中，我为 6 个软件产品的 43 个应用程序和服务贡献了 200 多个包，从高吞吐量 AWS 服务到面向客户的 web 应用程序，再到不经常更改的内部工具。

作为一个流浪者，我贡献了这么多不同的应用程序，每一个都持续了相对短的时间，我比大多数人更清楚“冷启动”开发体验的问题。

在过去几个月的另一次团队转移后，我再次意识到冷启动开发经验对软件产品的影响有多大，我也更加相信它值得跟踪和优化。

# 冷启动开发

我将应用程序的冷启动开发体验定义为工程师第一次或在长时间中断后用来更改应用程序的过程。

冷启动开发经验是“[工程师入职](https://refactoring.fm/p/onboarding-engineers?s=r)的一个组成部分，但仅限于软件开发生命周期。它包括授予源代码权限、访问加密密钥、数据库或其他资源、一次性配置、引导软件、依赖项、部署工具和硬件。

在这种情况下，冷启动是一个典型的新团队工程师对应用程序进行最简单的更改所需要的(想想:Hello World)。

我之所以使用“冷启动”这个术语，是因为这些天我一直在思考“功能即服务”产品的[冷启动](https://en.wikipedia.org/wiki/Cold_start_(computing))问题，功能和工程师之间的类比非常好。

![](img/022516929daa10504e8153bc8f3d1141.png)

[“运行λ:性能优化”](https://aws.amazon.com/blogs/compute/operating-lambda-performance-optimization-part-1/)

# 冷启动对软件的影响

在下文中，我认为应用程序的冷启动开发体验几乎影响了软件和软件团队的每个主要方面:生产率、可用性、质量、安全性、可替代性、士气和损耗。

## 工程师生产力

冷启动影响软件的明显部分是生产率。考虑到软件工程师的低平均任期和高离职率，一个普通工程师对一个产品的总贡献受到长期冷启动的限制。

如果一个普通的工程师只需要在一个拥有十几个不断变化的应用程序的团队中工作 18 个月，每个应用程序都需要每 6 个月左右进行一次为期一天的冷启动，那么业务的机会成本就是工程师在团队中的总时间的一个有意义的量。考虑到雇用工程师所需的努力，我认为每个团队每年花费在建立开发环境上的成本在数万美元左右。那是愚蠢的！

## 可用性和收入损失

对于包括软件工程师的随叫随到的产品，长时间的冷启动也是可用性风险。我见过不止一次停机时间被有意延长，因为电话技术人员花了几个小时努力设置服务来修复一个错误。

当一项服务具有严格的可用性服务级别协议(SLA)和[许多许多“九”](https://www.techtarget.com/searchnetworking/feature/The-Holy-Grail-of-five-nines-reliability)时，一个小时的浪费可能是毁灭性的。阅读一份[纠错](https://wa.aws.amazon.com/wat.concept.coe.en.html)时间表令人尴尬，其中包括随叫随到建立数小时的服务(忽略了这显然应该事先完成)。

考虑到每分钟停机损失的收入，长时间冷启动的成本可能是巨大的。

## 质量

当一个开发人员的任务是构建一个特性时，任何花费在让应用程序运行上的额外时间都会消耗掉分配给完成这个特性的时间。当时间紧迫时，质量往往首先受到影响。

此外，一次痛苦的冷启动设置经历导致了[破窗理论](https://blog.codinghorror.com/the-broken-window-theory/)对该应用的影响。如果应用程序已经花了 3 天时间来设置，那么再花 5 分钟又有什么意义呢？

受冷启动影响的软件质量的另一个因素是架构内聚性。在面向服务的架构中，长时间冷启动的一个不幸的副作用可能是短视的动机，将逻辑放在不属于它的地方，只是为了生产速度或避免摩擦。一天的障碍会损害建筑的凝聚力，这是一种耻辱，但它确实发生了。

## 安全性

回想一下最近引起互联网大火的高调 [Log4J/jndi 零日](https://www.wsj.com/articles/what-is-the-log4j-vulnerability-11639446180)。当每一秒钟都在增加 web 应用被破坏的可能性时，冷启动就是一个生存威胁。当开发人员第一次(在凌晨 3 点)想出如何构建一个团队中任何人一年都没有接触过的应用程序时，公共零日补丁的应用程序被延迟了几个小时，这很容易破坏信任。

![](img/b4f9db186066183b3ab5969a495a5ff0.png)

图片来自 log4jmemes.com

## 知识孤岛和工程可替代性

冷启动还会导致不必要的专业知识孤岛，这对[非常不利](https://www.pluralsight.com/tech-blog/knowledge-silos/)。根据我的经验，自组织团队会将任务分配给已经建立了环境的工程师，而不是将工作平均分配给可用的同事。通过将任务交给已经建立了开发环境的人来避免挂钟时间是很自然的，但从长期来看这对团队健康不利。

这种由应用程序设置引起的孤立可能是有害的。这使得一些工程师“被困”在系统的特定部分，仅仅是因为团队太懒而没有设置环境。根据我的经验，如果一个应用程序有一个很长的冷启动设置成本，它通常还伴随着其他糟糕软件的特征，比如高度耦合、复杂性和/或差的测试覆盖率，如上所述。

我不止一次地看到这种情况，我可以说，仅仅因为你最近设法让它运行起来，就成为那些糟糕的系统上的任务的第一选择会令人沮丧。

## 团队士气和损耗

最初安装软件的痛苦经历也是人员流失的一个因素。

当我还是一个陌生人的时候，我曾经看到一个新来的工程师突然“180 度大转弯！”在与一个特别讨厌设置的应用程序发生冲突后，离开团队，称设置困难是他们离开时的一个主要抱怨(尽管是几个抱怨之一)。

在上述 siloing 部分中提到，冷启动设置时间长的应用程序通常还伴随着其他不好的软件模式。冒着将软件拟人化的风险，首次环境设置中的摩擦会让应用程序看起来像是在积极地抵制被改变，从而导致不满和想要避开那个组件。

# 如何衡量和判断冷启动

工程领导可以简单地衡量这种冷启动:当他们对系统进行第一次更改时，观察一个新雇员或最近的调动。功能/错误修复分配和感叹“我终于让它运行了！”之间的时间是冷启动。

几分钟的冷启动是可以实现的，即使是我见过的最麻烦的遗留系统。天量级的冷启动比我想承认的更常见，也更不可接受。

如果你正在考虑团队变动，问问你未来的队友，要多久才能从“新笔记本，谁是谁？”到他们最被忽视的服务或应用程序的 CD 管道中。那个回答能告诉你很多！

# 如何提高

在已经有足够长的冷启动时间的应用中，可能有几十个明显的、容易实现的改进机会。

我鼓励任何仍在阅读的人使用这个框架来识别这些机会。

1.  问问新开发人员最痛苦的部分是什么。先改善一下。
2.  构建应用程序所需的引导工具可以和脚本一起安装吗？
3.  您的开发环境真的需要那种资源吗？考虑在本地模仿依赖项，使用不太安全的代理代替 SSL 证书(在开发环境中！)，使用静态而不是动态资源。
4.  收集并列出开发人员冷启动应用程序的步骤。花一天时间消除或自动化其中的一个。这样做一个月一次或一个季度一次，直到你不能。

# 结论和参考文献

这不是一个特别新的话题，但最近的经历迫使我抱怨它。

如果你想阅读更多关于这个主题的内容，可以看看 Joel Test 和这篇很棒的文章“[想要一个高绩效的开发团队吗？提高您的工作效率。](https://builtin.com/software-engineering-perspectives/increase-new-developer-productivity)

如果你觉得这篇文章有用，请留言、鼓掌、[或@我](https://twitter.com/zdwolfe)！