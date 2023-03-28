# 高质量的 ML —第 3 部分—安全的生命周期实践

> 原文：<https://medium.com/codex/high-quality-machine-learning-part-3-c660b885233c?source=collection_archive---------15----------------------->

*请看本系列*第一部 *和* [*第二部*](https://mukund-kannan.medium.com/high-quality-machine-learning-part-2-2643c144a2a7)

*![](img/1b6f9f9d6ae5dc13d53bcf1b53dd1e0c.png)*

*[范二·帕希兹](https://unsplash.com/@erfanprh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片*

# *保卫 DSLC*

*DSLC 的安全和威胁评估仍然是一个成熟的领域。在过去的几年中，DevSecOps 运动使人们更好地关注开发生命周期中对安全性思考的需求。早期(相当流行——仍然在大多数项目中)的方法“让我们在关键测试周期结束时获取代码，并通过一些代码扫描器来识别安全问题”已经被证明是不够的。*

*在对抗性的 ML 威胁矩阵([https://github.com/mitre/advmlthreatmatrix](https://github.com/mitre/advmlthreatmatrix))中可以找到对企业中 ML 模型的可能威胁的更广泛的覆盖(包括适用于 DSLC 的非 ML 系统威胁)。让我们从三万英尺的高度来看待 ML 项目中与安全相关的挑战。*

## *数据管理阶段*

*获取外部数据是有效数据科学项目策略的一部分。但是正如上面在公平性讨论中提到的，理想情况下，外部数据代理需要提供一定程度的数据完整性保证。美国国会推出的一项法案(2019 年数据经纪人名单法案)已经采取了初步措施，在联邦贸易委员会下引入一些监管。虽然该法案侧重于隐私，但它有可能解决一些关于数据完整性的问题。*

*数据摄取和准备可能是任何想从根本上下毒的人的温床。防止这种情况需要更大的数据治理策略所涵盖的更好的数据访问机制。虽然这是数据科学领域中讨论最多的主题之一，但随着时间的推移，实施变得越来越困难。方法多种多样，从旧式的基于表级别滚动的访问控制，到使用数据部分实时发现的高级方法&由动态策略驱动的自助访问。*

## *模型构建阶段*

*现在，让我们把那些直接威胁放在一边，比如接管完整的训练时间处理来开发一个恶意的 ML 模型。迁移学习是对手妥协最终 ML 模型的更容易的工具。狡猾的运营商可以通过 GitHub 库、模型动物园和 pickle 文件提供预先训练好的恶性 ML 模型。在我们生活的不受控制的世界中，这是一个明显的风险，在这个世界中，高度复杂的初始 ML 模型被大量重用。在开源世界中，信任是基于社区对代码的审查，而在共享 ML 模型中，这是不可能的。应仔细考虑预先训练的 ML 模型的出处，以减少此类威胁。*

## *部署管理(ILS)阶段*

*对抗性攻击理所当然地获得关注，成为活的 ML 模型的最大潜在威胁。精心策划的攻击者可以通过合法访问使用 ML 模型，建立对 ML 模型行为的理解，并使用这种理解向输入添加足够的(人类察觉不到的)扰动来欺骗 ML 模型。ML 模型可能被愚弄到将预测翻转到危险的对立面的程度。例如，让 ML 模型预测 55 英里速度限制标志，当显示停止标志时。*

*对这种攻击的防御包括模型构建周期期间的噪声注入、自监督方法以及基于 GAN 的方法。IBM 的 ART(对抗性健壮性工具包)是一个 Python 库，用于度量健壮性并强化 ML 模型。ART 涵盖了大多数众所周知的威胁和防御方法。Google 的健壮性指标是最近发布的一个库。它具有稳健性(处理扰动的能力)、分布外归纳(ML 模型处理未完全由训练数据表示的情况的能力)和不确定性(ML 模型预测的不确定性的度量)的度量。*

## *关键要点*

*   *战略上定义良好的数据治理是确保 DSLC 安全的基本要求*
*   *模型的重用和迁移学习是潜在的折衷点。在选择外部可重用工件时必须非常小心*
*   *必须使用可用的库(如 ART)来测量对抗性鲁棒性并强化 ML 模型*

# *结论*

*计划作为企业 IT 系统的一部分投入使用的“质量”模型，不能简单地用准确性目标来衡量。它必须在功能上准确无误，并且在游戏环境中表现良好。过去几年取得了如此大的进展，预计明年在这一领域会有更大的进展。*

# *承认*

*我在撰写这篇 3 部分文章时特别提到的或者在日常工作中获得的大部分知识，我都列在下面。除此之外，还有很多播客、在线视频和文章帮助我形成了自己的想法。不幸的是，这个列表太长了，没有用。我建议任何感兴趣的人通过下面的链接获得比我在这篇小文章中所能提供的更多的知识。*

*1.TFDV 文档—[https://www . tensor flow . org/tfx/tutorials/data _ validation/TFDV _ basic](https://www.tensorflow.org/tfx/tutorials/data_validation/tfdv_basic)*

*2.远大前程—【https://greatexpectations.io/ *

*3.ML 系统的有效测试—【https://www.jeremyjordan.me/testing-ml/ *

*4.自然语言处理的行为测试—马尔科·图利奥·里贝罗、吴同双、卡洛斯·盖斯特林、萨梅尔·辛格—[https://homes . cs . Washington . edu/~ marcotcr/ACL 20 _ check list . pdf](https://homes.cs.washington.edu/~marcotcr/acl20_checklist.pdf)*

*5.谷歌假设工具—[https://cloud . Google . com/ai-platform/prediction/docs/using-What-If-tool](https://cloud.google.com/ai-platform/prediction/docs/using-what-if-tool)*

*6.认知量表 Cortext 认证—[https://www.cognitivescale.com/certifai/](https://www.cognitivescale.com/certifai/)*

*7.ML 模型实践中的公平性—[https://medium . com/swlh/machine-learning-Model-Fairness-in-practice-bdebeaa 76 ee 8](/swlh/machine-learning-model-fairness-in-practice-bdebeaa76ee8)*

*8.数据伦理与创新中心—[https://cdeiuk.github.io/bias-mitigation/interventions/](https://cdeiuk.github.io/bias-mitigation/interventions/)*

*9.最著名的 AI 公平 360 框架—[https://github.com/Trusted-AI/AIF360](https://github.com/Trusted-AI/AIF360)*

*10.机器学习中的公平性教程-Ziyuan Zhong—[https://towardsdatascience . com/a-Tutorial-on-fairness-in-Machine-Learning-3ff 8 ba 1040 CB](https://towardsdatascience.com/a-tutorial-on-fairness-in-machine-learning-3ff8ba1040cb)*

*11.ML 幻灯片中的 NIPS 公平性(Solon Barocas 和 Moritz Hardt)——[https://mrtz.org/nips17/#/](https://mrtz.org/nips17/#/)*

*12.关于人工智能的非常有用的博客—[https://www . borealisai . com/en/blog/tutorial 1-bias-and-fairness-AI/](https://www.borealisai.com/en/blog/tutorial1-bias-and-fairness-ai/)*

*13.机器学习系统的架构风险分析:走向更安全的机器学习(Gary McGraw，Harold Figueroa 博士，Victor Shepardson Richie Bonett 博士)——【https://berryvilleiml.com/docs/ara.pdf T2*

*14.谷歌的稳健性指标—【https://github.com/google-research/robustness_metrics *

*15.对抗性准备工具箱—[https://github . com/Trusted-AI/对抗性鲁棒性工具箱](https://github.com/Trusted-AI/adversarial-robustness-toolbox)*

*16.2019 年数据经纪人名单法案—[https://www . congress . gov/bill/116 th-congress/Senate-bill/2342/text？r=5 & s=1](https://www.congress.gov/bill/116th-congress/senate-bill/2342/text?r=5&s=1)*

*17.对抗性 ML 威胁矩阵—[https://github.com/mitre/advmlthreatmatrix](https://github.com/mitre/advmlthreatmatrix)*