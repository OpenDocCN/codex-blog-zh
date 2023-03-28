# 单元回归测试是如何发现 NHS 新冠肺炎应用程序中的漏洞的

> 原文：<https://medium.com/codex/how-unit-regression-tests-could-have-spotted-a-bug-in-the-nhs-covid-19-app-18952a0ebc1a?source=collection_archive---------1----------------------->

## [法典](http://medium.com/codex)

![](img/40a6addef6784716d9e8e46aa2da78f8.png)

[单元回归测试](https://www.diffblue.com/documents/eBooks/Diffblue-Unit-Regression-Tests.pdf)是在开发过程的早期运行的测试，并且发现破坏过去工作的编码错误(即回归)。像 [Diffblue Cover](https://www.diffblue.com/products/) 这样的工具可以自动编写这样的单元回归测试。

软件中普遍存在回归问题的一个很好的例子出现在本周，当时 NHS 的新冠肺炎应用程序出现了一个错误(现已修复),导致它无法通知一些人他们应该自我隔离。

最初，该软件完全按照它应该的那样工作，但后来一个旨在改善该应用程序的软件更改破坏了它——直到几周后才有人注意到。

在怀特岛和伦敦纽汉区的测试中，该应用程序使用的统计模型得到了正确实施，用户也得到了适当的警告。但在更广泛地向公众发布之前，该模型得到了增强，以考虑到“传染性”——病毒携带者在最初出现症状时最具传染性的事实。

这提高了模型的准确性，这意味着可以降低风险阈值(用户可能具有传染性的“分数”),以捕捉更多的 COVID 病例，而不会导致更多的假阳性。在这种情况下，假阳性将是告诉用户在他们没有传播疾病的重大风险时进行自我隔离。

鉴于新的评分方法，风险阈值本应降低。但这一变化并没有被纳入已发布的应用程序中(从[政府健康技术博客帖子](https://healthtech.blog.gov.uk/2020/10/29/how-the-nhs-covid-19-app-is-making-the-most-of-cutting-edge-global-technology/)中还不清楚原因)。

## 单元回归测试有什么帮助？

单元回归测试可以帮助软件开发人员在开发时发现这类错误。在这种情况下，像 [Diffblue Cover](https://www.diffblue.com/products/) 这样的工具将编写两个测试来触发控制流的两端——一个是应该发送通知的情况，另一个是没有达到风险阈值的情况(没有发送通知)。有两种方法可以帮助开发人员在对代码进行新的更改时发现潜在的问题:

1.  单元测试失败，标志着一些以前没有发生的新行为(当代码工作时)。
2.  当 Cover 为新代码编写新测试时，比较旧测试和新测试会突出风险函数返回的值的变化。即使前面的测试没有发现这一点——单元测试并不是万无一失的——测试之间的差异突出了代码中的行为变化。简单地标记这种差异就足以提醒开发人员了。

单元回归测试可以提高开发团队在问题交付给用户之前发现问题的能力。

## 您可能还会对以下内容感兴趣:

*   [如何写出更好的单元测试断言](https://diffbluehq.medium.com/how-to-write-better-unit-test-assertions-15c7349a4b3a)
*   [用单元回归测试改进软件交付](https://diffbluehq.medium.com/improving-software-delivery-with-unit-regression-tests-b5a47abe9a8b)
*   [为什么回归错误如此重要？](https://diffbluehq.medium.com/why-do-regression-bugs-matter-so-much-f5fd931c1670)
*   [如何缩短变更提前期，提高变更失败率](https://diffbluehq.medium.com/how-to-shorten-your-change-lead-time-and-improve-your-change-failure-rate-f920365be14c)

*原载于 2020 年 11 月 3 日 https://www.diffblue.com**的* [*。*](https://www.diffblue.com/blog/testing/software%20development/how-unit-regression-tests-could-have-spotted-a-bug-in-the-nhs-covid-19-app/)