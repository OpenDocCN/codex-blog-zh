# 神奇宝贝数据分析

> 原文：<https://medium.com/codex/pok%C3%A9mon-data-analysis-372a095b65ae?source=collection_archive---------5----------------------->

![](img/5bbef45b8edd34938b2ce836a4348a92.png)

迈克尔·里维拉·🇵🇭在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我的目标是观察整个系列中神奇宝贝的类型分布，什么类型最强和最弱，以及哪个单独的神奇宝贝最强和最弱。

神奇宝贝拥有各种属性，有助于统计分析，如个人攻击，防御，特殊攻击，特殊防御，速度和健康统计。此外，它们至少有一种(通常是两种)元素类型。他们可以根据出道的年代、所属的蛋类、拥有的类型以及是否“传奇”来进一步分类。

一个额外的好处是神奇宝贝的数据是混乱的。精灵宝可梦数字作为索引工具毫无用处，因为多个精灵宝可梦有多个共享相同数字的形式。如果不考虑巨型进化(某些神奇宝贝的超级版本),就会扭曲统计结果。此外，根据数据集和你的目标，可能会有无关的数据，如身高，体重，蛋组(哪些神奇宝贝可以相互繁殖)，甚至颜色或类型匹配。虽然这些很有用，但在我的项目中并不需要。

我最初使用[这个数据集](https://www.kaggle.com/abcsds/pokemon)来自 Kaggle，包含了 1-6 代之间的所有神奇宝贝。它有 13 列:扑克指数#，名称，类型 1，类型 2，基础统计总数，基础 HP，攻击，防御，Sp。atk Sp。Def、Speed、Generation 和一个真/假的“传奇”列。长话短说，传奇神奇宝贝往往有更高的属性和更有利的打字。

在写这篇文章的时候，有 8 代神奇宝贝，所以为了增加剩下的 2 代，我用 Python 拼凑了一个网络抓取程序，把它们从 Bulbapedia 中抓取出来。那个节目已经湮没在时间里，不能在这里播放了。但是，我可以告诉您，该数据拥有较少的列，因此需要在 Excel 中进行修改，以使其与数据集的其余部分保持一致。例如，我必须为传奇专栏添加数据，以及神奇宝贝属于哪一代。将所有 8 代的 Pokédex 放在一个 Excel 文件中，我设置了一个表来将其导入 MS SQL Server:

然后我添加了一个区域 ID 列。“代”和“区域”通常是多余的，但当你遇到区域形式(如第一代引入的 Diglett 对第七代引入的 Alolan Diglett)和大型进化(在第六代推出)时，代就失去了一些效用。最初，数据集让巨型进化共享它们的基础神奇宝贝的世代，但我发现将巨型进化限制在它们各自的世代是有用的，如果不是完全过滤掉它们的话。

我肯定有更性感的方式来做这件事。

接下来是手动输入哪些神奇宝贝是“传奇”如果你问大多数神奇宝贝粉丝，像 Mew、雪拉比、Zapdos 和基拉祈这样的神奇宝贝都符合条件。然而，许多被西方商品称为“传奇”的神奇宝贝实际上是“神话”的神奇宝贝。区别很讨厌，意义不大，但是原始数据集使用了传说中/神话中的区分。出于统计的目的，我发现这可能会扭曲类型范围分析的结果，因为神话神奇宝贝仍然比非传奇/神话神奇宝贝有更高的统计分布。将来，更好的做法可能是在一个专栏中创建 3 个类别:传奇，神话，两者都不是。

当时，将所有“特殊的”神奇宝贝放在一起，并让列以二进制值的形式存储它们的传奇状态，感觉更简洁。这需要了解哪些神奇宝贝属于哪个类别，但数据集足够小，手动输入并不麻烦。

接下来，我必须为所有的大型进化修复大型条目。原始数据集将所有大型神奇宝贝作为“NameMega Name”(例如 VenusaurMega Venusaur)。我更新了该列，在所有带有“Mega”或“Primal”的姓名条目之间添加了一个空格，并将它们的代设置为 6。然后，我将神奇宝贝和梅卡阳玛切换回它们正确的世代，因为我知道前面的查询会改变它们。

然后，我将该表一分为二:一个表包含每个神奇宝贝的统计数据，另一个表包含名称、世代、地区和传奇状态。为了以防万一，我还创建了一个复制原始组合表的视图。对于这么小的数据集来说，这些步骤不是必需的，但是对于数据库设计来说，这是一个很好的实践。这也使得 Power Pivot 更容易使用。

在这里，我纠正了一些我在最初导出到 Excel 时注意到的小错误。这突出了神奇宝贝的一些细枝末节，我认为你不会明白，除非你没有生活(像我一样):

我还犯了一个令人尴尬的新手错误，忘记为错误的空格修剪类型列，导致了额外的类型组合:

我还创建了一个排除大型进化的视图，并更新了数据库，增加了两个丢失的怪物:

现在是时候将这些数据导出回 Excel 了。

最初，我用传统的方法做了以下工作——在电子表格中进行一系列的逐行计算。它很笨重，很难看，它让我的预算笔记本电脑在运行计算时发出尖叫，而且这个文件因为有 38 兆字节而要花很长时间才能打开。它起作用了，但是我不满意。

![](img/2bd548da357005ebc6c64570b8a51611.png)

这又慢又难看。

我决定使用 Excel Power Pivot 和数据模型。权力支点岩石，即使界面是一个噩梦。它允许您使用各种来源的数据创建数据模型，然后允许您运行在 Excel 中无法完成的各种计算，同时占用更少的空间。我的最终文件，可视化和所有的，仅仅超过一兆字节。

为了得到我的答案，神奇宝贝和组合类型哪个最强大，简单的总结什么类型的神奇宝贝属性最高是不行的，因为许多神奇宝贝共享相同的基础属性总数。

![](img/be4ba42fbcd7d399e2ea40ad840b128c.png)

在这里，我们看到了令人头痛的背信弃义的神奇宝贝形式。

相反，我需要获得每个战斗统计的 z 分数，以便说明每个战斗统计的差异。这意味着获得每个统计数据的平均值和标准差，随后的标准差公式以这样一种方式设置，即结果将根据最终用户决定的筛选标准进行更新。不幸的是，在博客文章中没有有趣的方式来展示权力中枢，所以我们将直接跳到结果。请记住，这篇文章中的图片将被压缩。我强烈建议您点击完整工作簿的链接，这样您就可以看到原来的仪表板并与之交互。

首先，让我们看看整个系列的总类型分布:

![](img/77ef6082f7c507323d084faff327fe22.png)

快速浏览一下，我们会发现水和正常类型是最受欢迎的，远远超过其他类型。此外，正常飞行是最常见的类型组合。以下是一些没有被压缩扼杀的不那么令人印象深刻的图表:

![](img/01be476b69f76bce8b3d12c0f041c1cf.png)

水是最受欢迎的初级打字，飞行最少。

![](img/aa6773722ee870c8290d9389996f52db.png)

飞是最受欢迎的辅助打字，差远了。

所以正常飞行是最受欢迎的类型组合，正常和飞行似乎有一个相反的关系。Normal 是最受欢迎的主要类型之一，但却是最不受欢迎的次要类型。相反，飞行是最受欢迎的第二种打字方式，但却是最不受欢迎的第一种打字方式。只是一个简单的观察。

还记得我说过神奇宝贝数据乱七八糟，神奇宝贝数字没用吗？这篇文章中每张图都有一个有趣的警告:我没有删除神奇宝贝表格。对于统计数据的分析，这不是一个大问题。然而，当将平均值和总数制成表格时，事情就变得棘手了。例如，并没有真正的 11 个鬼草类型的神奇宝贝。然而，同样的两种鬼草有不同大小的版本。每一个都有自己独特的统计数据。

![](img/a905529649404bf17a63a3c6892816a9.png)

一个类似的例子是代欧奇希斯堵塞了通灵类型的行列。此外，代欧奇希斯的表格都有专门的统计分布，这可能会影响统计分析:

![](img/5594cc0a2515a45a365245afa08ab442.png)

那只是冰山一角。还有其他有条件或地区形式的神奇宝贝。很容易证明去除大型进化是合理的；它们是定义良好的高统计条件转换类别。很难证明移除代欧奇希斯形态或者仅仅选择一个南瓜或者美食家的尺寸，或者移除禅宗模式的达摩狒狒是合理的。在这一点上，它只是吹毛求疵什么留下什么去。

好吧，那么什么样的打字最有效呢？使用每种类型组合的基础统计总数的中值 Z 得分作为总统计“强度”的度量，结果如下:

![](img/17cbc94536e613c3adb82f0f7c006576.png)

这么多年了，灵媒和龙还在上面。

对于任何玩游戏的人来说，这些结果并不令人惊讶:龙和通灵拥有最高频率的强力类型组合。这两种类型在传说中的神奇宝贝中都很常见，而龙是最罕见的类型之一，也是最强大的类型之一。考虑到这一点，什么类型的组合是最强大和最不强大的呢？

![](img/b6361add25a9733afb4b5357ade101ae.png)

Bug 抓不住突破口。

通灵者和龙主宰了最强类型的列表，臭虫畏缩着屈服了。然而，重要的是要记住，这些列表包括传奇，通常拥有罕见或独特的类型组合。例如，唯一的龙冰类型是酋雷姆，一个强大的传奇人物，有三种不同的形态，其中两种有非常高的基础属性总数。如果没有传奇故事，这个列表看起来像这样:

![](img/5e8381c7a7018bfdd3f1464a8bac0803.png)

Bug 还是抓不住突破口。

Bug 依旧全军覆没，但至少在精英中得到了一个代表(感谢，Heracross！).火和摇滚也有更多的空间来展示他们的肌肉，而没有巨人在周围跺脚。

最后，根据总 z 分数强度，整体神奇宝贝最强和最弱是什么？

![](img/5199b1866af79a3cd8a49d0e5325ccb1.png)

哎呀，都是 Ubers！

好吧，更好的问题，最强的非传奇怪物是什么？

![](img/9fdb055d12bb7a186df5cbdd4376e04a.png)

对于没有玩过这款游戏的人来说，这是一个惊喜。

十个最弱的呢？

![](img/ff0a49f6db0ede44b897ec9619653c90.png)

这几乎是一比一最弱的基础统计总数。

好吧，我们学到了什么？即使根据传说和他们荒谬的统计数据进行调整，龙仍然是最强大的类型，并且在你的 z 分数排名中占了十大最强神奇宝贝的大部分。与此同时，Bug 类型被压倒性地放到了最底层。显然，这并不能说明全部情况:基础统计和统计分析忽略了神奇宝贝可用性的其他重要方面。在决定神奇宝贝的生存能力时，诸如动作组合、分类、持有物品和能力之类的东西都扮演着同样重要的角色(这也是为什么没有人使用猛击)。不过，有趣的是，前 10 名中的大多数神奇宝贝都曾经出现在竞争激烈的战场上。

嗯，这比预期的要长得多。如果你想自己试验一下结果，以及为了简洁起见我省略掉的一些额外的图表和表格(是的，我知道)，我强烈推荐点击下面我做的 Excel 仪表盘的链接。散点图有交互功能，除非你把它下载到你的桌面上，否则无法工作，但其他一切都应该工作正常:

[](https://1drv.ms/x/s!Avmh8oOwgbrtgcRZGsgq8HJnFl3ttw?e=0lTBGu) [## PokemonProjectRevised.xlsx

### 在线存储照片和文档。从任何 PC、Mac 或手机访问它们。在 Word、Excel 或……上创建和协同工作

1drv.ms](https://1drv.ms/x/s!Avmh8oOwgbrtgcRZGsgq8HJnFl3ttw?e=0lTBGu)