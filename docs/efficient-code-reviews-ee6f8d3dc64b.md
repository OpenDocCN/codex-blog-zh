# 高效的代码审查

> 原文：<https://medium.com/codex/efficient-code-reviews-ee6f8d3dc64b?source=collection_archive---------5----------------------->

代码评审和代码评审有很大的区别。知道如何有效地做这件事是非常重要的。我们应该能够从“LGTM，认可”变成不是负担而是资产的东西。

![](img/1fe0cd02db4fa8e7fbd9df862ddf6a74.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

关于公关评论，为什么他们是重要的，他们如何工作的文献是相当广泛的。我不会花太多时间在这里讨论这些。相反，基于我的经验，我想涵盖主要的反模式和陷阱，这些反模式和陷阱通常会破坏和减慢代码审查。自然，什么被认为是好的评审(或者代码评审过程)根据上下文的不同而不同。也许你在松散耦合的团队中工作，或者在由社区驱动的开源项目中工作，或者也许时间不是真正的限制，或者也许你在一个有效的紧密协作的团队中工作，除了质量问题，你还有严格的截止日期，就像常规的企业项目一样。我的观点和例子与后一种情况一致，但这些不是普遍的事实；它们只是我在多家公司的多个项目中发现的常见模式和简单要点，所以这只是我的观点，请照此看待。

# 范围

## 目标

评审应该针对较低的级别，更接近变更的代码方面。主要目标是验证代码是否遵循团队使用的指导方针和约定。基本上，静态代码分析器无法覆盖的任何东西，如代码中实体的结构化和命名，简化或抽象某些东西的机会，合理的优化(众所周知，为优化而过早优化是所有罪恶的根源)，使用“手动”完成某些事情的库，利用更新的语言功能，注意多层次的测试…你得到了要点…你知道较低层次的东西。它还应该更加关注“我们如何做某事”，而不是“我们在做什么”。不要误解我的意思，这些讨论真的很重要，但是如果它们发生在公关层面，那就意味着已经在团队不同意的事情上投入了很多努力。

## 问题的迹象

关于 PR 的讨论实际上不是特定于代码的，它更接近于架构级别，例如:“虽然这种变化是关于身份验证的，但我真的认为我们应该改变我们的方法并利用 CQRS”，或者是否不断提出不同的框架来解决底层问题。

## **可能的补救措施**

明确的要求，如果有必要，召开团队设计会议，或者如果不可能，至少应该就方法达成高层协议。这样，每个人都在如何实现该功能的同一页上，因此只有较低级别的细节丢失。是的，我知道这对于很小的改变毫无意义。我觉得这里用常识是可以的:)。在整个代码评审流程中，拥有明确的角色和职责的指导方针也是很有帮助的。在团队层面上就它们达成一致，并记录下来或采用现有的。这将是一个很好的参考新加入者。

# 服务等级协议

## **目标**

每个人都应该努力使代码审查简短、切中要点、清晰，目的是提高质量，使代码库更加一致，同时在长度上是可预测的。不是让我们错过最后期限的事情。它应该是核心开发程序的一部分。当代码评审完成时，每个人都应该很清楚，没有不必要的相互等待或不断“追捕”和唠叨人们做他们的工作。令人惊讶的是，这并不总是那么明显。

个人意见应该与团队利益一致，评审结果应该是可操作的。导致批准/合并或清除返工请求。如果不是这样，评审者和被评审者很容易互相误解。

## **问题的征兆**

最主要的是“文本墙”类型的评论。如果你不得不输入一部小说来证明一个观点，这意味着要么它不是一个很强的观点，要么 PR 在概念上出了问题，你正在解决一个更深层次的问题(详见“范围”部分)。

这种问题也可能出现在冗长的讨论中。随着人们变得越来越有经验，他们往往会有更强烈的观点，他们不会害怕分享这些观点。如果非常资深的人有不同的偏好，他们在公共关系上相遇，可能会导致无休止的来回。当他们开始引用反对的文章来证明他们的观点时，那就是事情可能会失控的时候，你永远不知道审查什么时候结束。例如:“我读过这些文章，看过这个视频，它说抽象类不好…是的，但我已经读过这个和这个，它清楚地表明这些解决了一切”

罕见但大量的 PRs /许多跟进 PRs 也很好地表明团队的评审流程没有按预期工作。

## **可能的补救措施**

在应完成第一轮审核的时间之前，在打开 PR 后就团队级别达成一致。我们应该着眼于几天而不是几周或几个月来完成一个审查周期。如果评论/帖子太长，无法控制，澄清会议可以省去很多麻烦。从异步讨论转移到同步讨论有助于消除讨论障碍，加快讨论速度。

当心确认偏差。如果你有自己的观点或偏好，你可以找到支持你观点的文章。通常，决策是在好的或者至少是可接受的方法之间做出的，因此团队只需要得出关于哪种方法更适合的结论。

如果打开更小的 PRs，会使作者和审阅者的生活更容易。我想这是不言而喻的为什么。在设计阶段，将任务分割成小块需要更多的前期投资，但从长远来看，这是值得的。

不要只留下模糊的评论，要明确这些是否是阻挡者，或者你可以继续只是想分享一些想法。

# 动机

## **目标**

团队中的每个人都应该参与代码审查。这对团队来说是固有的好处，但对评审者来说可能没有那么明显的好处。人们互相学习，不仅通过写代码，还通过评审来学习团队编码实践，所以他们应该想做评审，而不是被迫去做

一般来说:停止开始，开始结束。不要让公关堆积如山。

## **问题的征兆**

这非常简单:与其他人相比，某人并没有真正地审查代码，或者他/她总是被要求或被烦扰去审查。如果你听到“如果我开始审查代码，我的功能会迟到”或“我对应用程序的这一部分不太熟悉，我没有必要审查”，这意味着个人的动机有问题。

PRs 被打开了很长一段时间，并且开始堆积，这再次很好地表明有些事情不对劲。

## **可能的补救措施**

需要明确的是，审阅一份公关和撰写相应的专题一样重要，都是人们日常工作的一部分。虽然这是一种干扰，可能是一种痛苦，但这项工作对团队的交付有即时和直接的贡献，并可能解除其他人的阻碍，因此它在团队层面上可能更加重要。

你也可以改善这种情况，如果你注意在完成一个 PR 时不要立即接新任务，直到至少另一个请求没有被审核，或者你可以引入小规模/友好的竞争或奖金来促进参与审核。我认为吹牛的权利被低估了。

# 所有权

## **目标**

所有参与方都应该清楚谁是有可操作责任的人，这样公共关系才不会空转，总有人在推动它走向合并。所以所有权的转移应该是明确的。应避免在任何给定时间拥有多个所有者。如果已经打开了一个 PR，那么团队的首要任务应该是尽快合并它

## **问题迹象**

请求长时间处于打开状态，因此并发打开的 PRs 数量正在增加。你会听到类似“你有时间去看一下吗？它已经开了好几天了。什么？你在等我吗？我认为它仍在进行中/被其他人覆盖/错过了关于它的通知…“

## **可能的补救措施**

注意所有权可能丢失的过渡点。就团队级别的“自动流程”达成一致，例如:在 PR 打开后，谁分配审核者，审核何时完成，谁通知谁，谁正在合并，以及何时…

# 重新审查

## **目标**

公关复审应该是确认你的意见得到了处理，而不是从头开始

## **问题的征兆**

作为评审的结果，整个 PR 被重写，因此每个新的评审迭代都是完全成熟的新评审。有关原因的详细信息，请参见“范围”部分。由于经常遗漏或误解的评论，还可能有许多重新评审的迭代。如果在重新评审期间，新的注释被添加到第一次已经存在的代码部分，这也是有问题的

## **可能的补救措施**

在重新评审期间，试着只验证为解决您的评论而引入的变更，当然，可能会有您在第一次迭代中遗漏的东西，但是如果这成为一种模式，评审可能会变得没完没了。如果情况变得不可收拾，打个电话总是有帮助的。一般来说，在评论中要清楚你的要求，如果可能的话，提供代码片段，这有助于以一种难以误解的方式表达你的想法。

总之，适当的代码审查不仅仅是为了代码最终发布而必须做的事情，它还是帮助团队作为一个实际的团队运作，而不仅仅是其各个部分的总和，同时帮助团队成员不断成长和相互学习的重要工具。查看他人的简历，阅读团队成员的评论，可以帮助人们成长，成为有凝聚力的团队的一员。因此，关键是要做好这件事，并给予它应有的重视。一如既往，没有灵丹妙药，也没有普遍真理，每种情况都是不同的，所以当涉及到审查代码时，目标是要注意你做什么以及如何做。