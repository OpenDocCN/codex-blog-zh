# 软件开发中最大的神话

> 原文：<https://medium.com/codex/the-biggest-myths-in-software-development-c102b4f9670c?source=collection_archive---------5----------------------->

## 在我的工作经历中，我已经看到了很多对软件开发的误解，这些误解对团队和公司都是有害的。

有些可能是主观的，有些可能会挑战你的信仰，所以我希望你保持开放的心态。我建议探索这些链接。

![](img/fc900b73f70938fe54936455f22a1133.png)

布雷特·乔丹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 产品

1.  ***【功能越多越好】*** 。最好的工具不是功能多的；是更符合用户需求的那种。拥有所有的功能使你的产品对每个人来说都是普通的。“[特性蔓延](https://en.wikipedia.org/wiki/Feature_creep)”阻碍了 UX、营销和[代码库可维护性](https://mogest.medium.com/why-your-huge-tech-team-isnt-delivering-3851be27712c)。*复杂性可能随着功能的平方而增加:功能数量增加一倍，复杂性增加四倍。提供十倍的功能，增加一百倍的复杂度。*——[日常事物的设计](https://www.goodreads.com/book/show/840.The_Design_of_Everyday_Things)。
2.  *“一个特性是其发展的代价”。我们都听过“但这只是一个按钮”。当构建一个新特性时，除了开发成本之外，还有很多其他的东西；还有硬件成本、代码库的额外重量，以及对文档和培训的影响，[还有许多其他固定和可变成本](https://twitter.com/johncutlefish/status/1335822976957247489)。*
3.  ****【一个产品总是需要说明书】*** 。用户界面不应该依赖于某些用户手册，添加说明只是权宜之计。如果你需要解释，那你就做错了。试着解决根本问题。进一步迭代设计，而不是坚持第一个解决方案；[以自给自足为目标](/codex/whats-an-autonomous-ui-c22dd01ccf9f)。*
4.  ****“如果用户不能完成某些任务，那是他们的错”*** 。这是最常见的[设计神话](/codex/product-design-for-non-designers-7f3e22f4aefa)之一。如果你的用户不能完成某个任务，团队需要承认它，从中学习，并迭代使它更有用和更包容。*
5.  ****“产品拥有者知道顾客想要什么”*** 。对了解客户需求的内部人员(产品负责人、产品经理等)持怀疑态度。他们不是[权威](https://yourlogicalfallacyis.com/appeal-to-authority)，也没有针对客户需求的水晶球。持续的[用户研究](https://www.interaction-design.org/literature/topics/user-research)和反馈是基础[或者你只是在猜测](https://twitter.com/jmspool/status/1542259507610411008)。*

# *过程*

1.  ****【估计重要】*** 。估计通常是浪费时间和焦虑的来源。估计通常是错误的。人们为了一个 1 或者一个 3 在漫长的耗费精力的会议上毫无意义地争吵。用这些能量来更好地理解故事，并把它们分成有价值的小块。一旦它们变小了，它们的大小就差不多了。*
2.  ****“敏捷/精益就是把事情做得又快又脏”*** 。敏捷不是提高速度或降低质量。这是通过定期向用户交付小价值并根据他们的反馈采取行动来降低风险。[精益](/codex/towards-lean-software-development-24460340b11a)是一套 7 项原则，包括减少浪费、将决策推迟到[最后负责的时刻](https://effectivesoftwaredesign.com/2014/03/27/lean-software-development-before-and-after-the-last-responsible-moment/)以及团队授权。*
3.  ****【敏捷是一种方法论】*** *。* [敏捷](https://agilemanifesto.org/)软件开发是*只是*关于我们日常处理问题的方法；它是以用户为中心的开发，授权给团队，交付[小步骤](/codex/splitting-user-stories-20475cb1769b)，实验文化，减少浪费，等等。一些方法体现了它的一些原则和实践(例如 [XP](https://en.wikipedia.org/wiki/Extreme_programming) ， [DevOps](https://en.wikipedia.org/wiki/DevOps) ，[看板](https://en.wikipedia.org/wiki/Kanban)，[精益](/codex/towards-lean-software-development-24460340b11a)， [CI/CD](https://en.wikipedia.org/wiki/CI/CD) )，但是敏捷主要是一种思维框架；不是一套规则。你没有实践敏捷；相反，你要么敏捷，要么不敏捷。*
4.  ****“做 Scrum 就是敏捷”*** 。敏捷不是一种方法或认证。你可能会照章执行 Scrum，但仍然不够敏捷。敏捷恰恰相反:它不断地适应。*
5.  ****使用 JIRA 是敏捷的***。[敏捷不是工具](https://twitter.com/jcesarperez/status/1448688484110282761)；这是一种跨公司的心态。从定义理想的原则、工作方式和流程开始。工具是结果，应该相应地选择。*
6.  ****“我们应该让系统为未来做好准备”*** 。未来太不确定，不能浪费时间在前期的大设计(和大路线图)上。当您有 10 个客户端时，为什么要为 100 万个客户端准备系统呢？[不要未来的](https://george3d6.medium.com/stop-future-proofing-software-c984cbd65e78)。关注当下的需求。理想情况下，你应该做完成当前目标所必需的最少工作，但是要留有选择的余地，并且整个系统要为变化做好准备，只有很少的限制(例如，代码库是精简的，但是容易发展)。让改变变得容易，拥抱不确定性。*
7.  ****【需求必须明确】*** 。*需求*这个词太过规范，因为它排除了某人为团队定义和规定解决方案。理想情况下，用户故事只是用户问题的占位符。该团队应该能够找到解决方案，并且值得信赖。这个想法假设团队遵循[以用户为中心的](https://www.interaction-design.org/literature/topics/user-centered-design)方法，拥有与客户/用户一起调查故事的技能。*
8.  ****“增加开发人员让项目进行得更快”*** 。这是一个在[神话人月](https://www.goodreads.com/book/show/13629.The_Mythical_Man_Month)中颇有阐述的经典神话([九个女人一个月生不出一个宝宝](https://en.wikipedia.org/wiki/Brooks%27s_law))。更多的人意味着更多的入职、更多的沟通渠道和更多的误解。我所在的最好的团队都很小。在一个公司层面，如果 [WhatsApp 在只有 50 名工程师的情况下扩展到 10 亿用户](https://www.quastor.org/p/how-whatsapp-scaled-to-1-billion)，为什么你的创业公司需要 300 名工程师？*

# *角色*

1.  ****【团队应有前端和后端】*** *。当敏捷的支持者吹捧自治的和多学科的团队时，他们的意思是这些团队拥有将价值放在用户手中的一切。按技术拆分团队，使他们相互依赖，需要大量对齐，从而产生大量[浪费](https://theleanway.net/The-8-Wastes-of-Lean)。此外，后端和前端的分离意味着[你只是在创造技术故事](https://levelup.gitconnected.com/stop-creating-technical-stories-f5e7bc424ff8)。**
2.  *****QA 是一个单独的阶段”。*** [QA 不应该是由特定的人控制的单独阶段](https://twitter.com/allenholub/status/1637159759660077056)(那会像在专门的阶段做性能调优一样尴尬)。许多团队甚至没有 QA 人员，但他们肯定有 QA。质量是文化的一部分，而不是事后的想法。如果你的板上有一个“准备测试”栏，那就是过程的味道——你在做一个两周一次的瀑布。QA 是一项技能，[应该根植于整个团队所做的每一件事情中——你需要自动化测试、TDD、适当的 CI/CD、总是以小的和可恢复的变化交付、每个人都有能力做验收测试，等等。](https://lsoares.medium.com/our-approach-to-quality-in-volkswagen-software-dev-center-lisbon-72f5728e2235)**
3.  *****“团队需要老板”。*** 这是[硅谷“得到的”传统公司没有的关于软件工程师的东西](https://blog.pragmaticengineer.com/what-silicon-valley-gets-right-on-software-engineers/)。团队是由高收入的成年人组成的，他们应该被信任去做他们的工作，包括自我管理。经理可以启用、指导和解锁。**
4.  *****“一个 scrum 高手就像一个经理”*** 。我听说过一些公司错误地认为他们变得敏捷了，但是他们所做的只是把经理们重新命名为 Scrum masters。Scrum 大师并不凌驾于团队之上，也不管理团队或项目。它只是在那里打开障碍，实现和指导敏捷性。**
5.  *****“企业应该保护开发人员。”团队不需要远离客户和用户，因为理解是他们工作的一部分。与客户交谈的核心人物是单点故障。开发者不是编码猴子，他们也可以帮助用户研究等等。*****
6.  *****【设计即服务】*** 。团队应该拥有交付用户价值的自主权，这包括设计。因此，设计不应该脱离团队。设计团队是个坏主意(就像一个医生团队和另一个护士团队)。**
7.  *****【设计只给设计师】*** 。没有；不是的。设计是团队关注的问题(就像 QA 和安全一样)。查看“[非设计师产品设计](/codex/product-design-for-non-designers-7f3e22f4aefa)”了解更多细节和相关神话。顺便说一下，设计不仅仅是制作图标；比如包括用户调研。**
8.  ***[***devo PS 是一份工作***](https://www.linkedin.com/pulse/devops-cultural-shift-job-title-nathan-rasch-mba-pmp-safe-sm-csm/)*。这不是系统管理员或 SRE。DevOps —开发和 IT 运营—是一个思想流派。它建议开发者应该被授权来构建、部署和维护他们的应用。你建造它，你运行它。DevOps 的原则之一是自动化，它包括 CI/CD 实践。****
9.  ******“团队不该浪费时间面试候选人”*** 。谁是比每天和员工打交道更好的面试官？团队外人对团队文化和需求知之甚少。这个团队应该被授权、训练和信任去采访人们。那不是浪费团队的时间；这是一项投资。***

# **发展**

1.  *****“K 公司用；因此我们要有它的*** 。这是一个最有害但又反复出现的误区。没有科技银弹。(例如“Monzo 有微服务，因此，我们需要它们”)。[你没有他们的问题，就不要带他们的解决方案](https://justsharing.dev/)。[不需要微服务](https://itnext.io/you-dont-need-microservices-2ad8508b9e27)。产品不是游乐场。如果你想尝试什么，就把它作为一个附带的实验。这个神话也适用于产品特性、团队工具和方法论(例如，“让我们实现 [Spotify 模型](https://www.atlassian.com/agile/agile-at-scale/spotify)以带来敏捷性”)。**
2.  *****【重构是一项独立的努力】*** 。正如 [Ron Jeffries 所说的](https://twitter.com/RonJeffries/status/1453102208887709700)，“如果有人认为重构属于积压工作，他们可能不理解重构，或者积压工作，或者两者都不理解”。重构应该是具有用户价值的故事的一部分。不要创建重构故事，因为它们是伪装成用户故事的技术任务。**
3.  *****“QA 意味着手动测试一切”*** 。DevOps 文化建议将所有经常重复和容易出错的工作自动化。开发人员应该编写自动化测试，因为手动测试一切都不可伸缩，并且与[连续交付](https://en.wikipedia.org/wiki/Continuous_delivery)不兼容。QA 专家应该做[探索性测试](https://en.wikipedia.org/wiki/Exploratory_testing)来代替。**
4.  *****“测试越多越好”*** 。客户不买测试的账。它们很基本，但也有成本(如维护、测试时间)。做最少的测试，为你提供安全、文档、问题定位和重构支持。如果你使用 [TDD](/codex/how-to-write-a-test-using-tdd-b2828788d7ea) ，那部分是有保证的(在某些情况下你可能需要做一些清理)。一个相关的误解是“100%代码覆盖率”意味着一个巨大的安全网。然而，代码可以用多种方式分解，重构时测试可能会很痛苦。**
5.  *****“共享代码是干的最好解决方案”。*** [复制远比错误的抽象](https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction)便宜。分享被高估了，所以[停止分享代码](/codex/avoiding-code-hotspots-a2bc5a8a967d)。共享是耦合所有特性的最佳方式，从而消除模块化。大多数可以共享的代码已经属于语言库和开源库。还有，[干适用于知识，而不是代码](https://www.linkedin.com/pulse/dont-repeat-yourselfdry-principle-mamata-raote/)。**
6.  *****“让我们建造一些通用的东西”*** 。这是最大的编码谬误之一。通用的解决方案让我想起了瀑布思维——让用户等待，得不到反馈，做了错误的事情……所以停止构建通用的东西，专注于小的迭代成功。我见过很多 UI 表有几十个过滤器，但是没人用。采用精益方法并不容易——这是一个精神飞跃，但任何人都可以做到。**
7.  *****“语言 X 将带来巨大的表现”*** 。一般来说，任何语言都可以。大多数情况下，性能瓶颈在于错误的架构决策(例如，加载所有数据库行，滥用服务间同步通信)。使用 Go 或 Elixir 无法修复这些决定。否则，我们都会用汇编语言编写代码。**
8.  ***【性能至上】。仅仅为了性能而优化代码可能是不好的。其他的因素也在起作用，比如可读性，这使得维护和构建新东西变得容易。只有当这是实际需求时，才进行性能优化。***
9.  ****[***微服务好；独石不好***](https://world.hey.com/joaoqalves/disasters-i-ve-seen-in-a-microservices-world-a9137a51?utm_source=pocket_mylist)*。这是一种极其狭隘的观点。如果 monoliths 被适当地模块化，它们可以拥有大多数微服务的好处，而没有(反)序列化、网络延迟、错误等。巨石柱是开始的最佳方式，在许多情况下，是继续的最佳方式。*****
10.  ******【代码总是需要注释】*** 。代码注释可能会过时、含糊不清，并且经常成为编写平庸代码的借口。评论本身并不是邪恶的*但是应该作为一个例外而不是一般的规则。以[自文档化代码](/codex/towards-self-documenting-code-371364bdccbb)为目标，尤其是在高级语言中。****
11.  ******“在生产数据库中进行查询是正常的”*** 。查询生产数据库不应该是一件事。除了所涉及的[恐惧](/codex/leveraging-technology-for-psychological-safety-48071fbd131e)之外，对数据库的直接访问会阻碍它们的性能，使你丢失数据，或者绕过你的业务领域规则。正确的方法是创建适当的工具来满足这些数据访问需求(例如，日志、脚本、API、GUI、CLI)。***
12.  *****“CI/CD 是运行管道的服务器”*** 。CI/CD 是一组支持持续集成、交付和部署的实践。除了必要的工具(包括 CI/CD 服务器，如 [Concourse](https://concourse-ci.org/) 或 Bamboo ),它还需要支持实践，如[基于主干的开发](https://trunkbaseddevelopment.com/)或短期分支，强大的自动化测试文化，应用程序监控，易于恢复等。所有这些都需要一种不同于常规的心态。**

# **开发商**

1.  *****“一个开发者需要一个 CS 学位”*** 。不一定。我见过没有计算机学位的了不起的开发人员。当然，CS 概念是基本的，但这并不意味着你不能带着热情和坚持不懈地学习它们。这种热情会影响你每天的态度，你学习的意愿，帮助他人的意愿等等。**
2.  *****“一个好的开发者掌握终端和 vi”。*** 这只是蒙面[守门](https://www.urbandictionary.com/define.php?term=Gatekeeping)。Vim，IntelliJ，终端，GUI，…都只是工具；不是生活方式。使用任何让你开心和有效率的东西，不要欺负别人。**
3.  ***[***10X 开发者神话***](https://www.simplethread.com/the-10x-programmer-myth/)*。这种开发人员是英雄/冠军原型，他们解决困难的问题，通常取悦老板。当一切都围绕着一个人时，你就有了瓶颈和单点故障。那些开发者也是知识的黑洞，所以他们应该投资于分享知识和帮助他人。****
4.  ******“资深开发者是在该领域有多年经验的人”*** *。大三和大四的划分是模糊的，而且通常是不必要的。为什么？成为高级软件开发人员并不是一个二元状态。你可能做了 10 年的错事。由于更多的努力，你可能会擅长更多的事情。很多“学妹”在具体的事情上可以是学长，很多“学长”可以跟“学妹”学很多东西。****
5.  *****“一个开发者只是一个编码者”*** 。你不是代码猴。代码行数不应该作为衡量一个开发者的标准。编码是工具，也是达到目的的手段。如果你不用编码就能解决用户问题，你就赢了。**
6.  ****《一个开发者单干》**:没有！这个神话源于开源开发和早期的硅谷创业公司。在正常的产品开发世界中，开发人员应该与其他开发人员以及整个团队一起工作。他们也可以帮助用户研究。**

# **结论**

**我希望我能够挑战你的一些信念。我确信你发现了例外，但是主要的想法是让你思考它们。以下是一些供进一步阅读的材料:**

*   **[*现代软件工程*](https://www.goodreads.com/book/show/57345270-modern-software-engineering) (戴夫·法利)**
*   **[*有效软件开发组织的启发式*](https://holub.com/heuristics/) (Allen Holub)**
*   **[*为什么你庞大的技术团队没有交付*](https://mogest.medium.com/why-your-huge-tech-team-isnt-delivering-3851be27712c) (罗杰·奈斯比特)**
*   **[*关于软件工程师，硅谷“得到”了传统公司所没有的东西*](https://blog.pragmaticengineer.com/what-silicon-valley-gets-right-on-software-engineers/)**
*   **[*如何走得快*](https://quii.dev/How_to_go_fast) (克里斯·詹姆斯)**