# 让我们都来欢迎 Excel 中的 Office 脚本在网络上的自动化，在每个平台上

> 原文：<https://medium.com/codex/lets-all-welcome-office-scripts-in-excel-on-the-web-for-automation-on-every-platform-745d5db488ee?source=collection_archive---------17----------------------->

## 微软的新脚本语言会取代最可怕的 VBA 吗？

* [堆栈溢出开发者调查](https://www.businessinsider.com/stack-overflow-developer-survey-10-most-dreaded-programming-languages-2020-5?IR=T)

高度自动化的工作场所与简化的流程、生产率、工作满意度和增加的创造力时间正相关。因此，无论有没有编码，日常任务的自动化比以往任何时候都更加重要。在我以前的帖子中，我提供了[和 VBA](https://burak-piskin.medium.com/five-handy-excel-macros-to-take-boring-chores-out-of-your-workday-7a6ab888ae47) 的例子，也认为[利用 Excel](/geekculture/harness-the-power-of-power-query-in-microsoft-excel-with-no-coding-for-your-repetitive-tasks-d12ee2fe9c26) 的现有功能和不同的方法可以用更少的努力和编码为我们的任务带来附加值。在这篇文章中，我们将发现另一个更快乐的工作场所的机会。

![](img/f50a131d3842a996ccfc14f0ad3eabef.png)

照片由[布鲁克·卡吉尔](https://unsplash.com/@brookecagle?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

## **什么是 web 上 Excel 中的 Office 脚本？**

首先，Office 脚本目前只适用于 web 上的 Excel。换句话说，我们不能将它用于其他微软工具，如 Word、Powerpoint 等网络上可用的工具。就技术方面来说，是用[打字稿](https://www.typescriptlang.org/docs/)写的。但是，我们仍然可以使用“动作记录器”在 Excel 中记录我们的动作，并通过“代码编辑器”进行编辑。注释会自动包含在代码编辑器中，在我看来，这是一个非常清晰和方便的特性。微软提供了大量的资源，如[概述](https://docs.microsoft.com/en-us/office/dev/scripts/overview/excel#when-to-use-office-scripts)、[设置](https://docs.microsoft.com/en-us/microsoft-365/admin/manage/manage-office-scripts-settings?view=o365-worldwide)、[文档](https://docs.microsoft.com/en-us/office/dev/scripts/)、[最佳实践](https://docs.microsoft.com/en-us/office/dev/scripts/develop/best-practices)、[教程](https://docs.microsoft.com/en-us/office/dev/scripts/tutorials/excel-tutorial)以及 Office 脚本的[示例](https://docs.microsoft.com/en-us/office/dev/scripts/resources/samples/samples-overview)。

## 我们有在 VBA 写的很酷的项目。它工作了。那么，我们为什么要用它呢？

嗯，我同意。但是，我有一些问题:

1.  VBA 只限于桌面环境，不能运行网络服务。我们比以往任何时候都更加紧密地联系在一起，并不断地与 web、iOS 和 Android 平台合作。你认为在不久的将来，尝试只在你的桌面上运行你的酷项目而不连接其他网络服务还能继续吗？
2.  VBA 宏可以完全访问您的桌面，即您的机器。Office 脚本只能访问工作簿。此外，Office 脚本管理员对脚本的共享和使用有更严格的控制。您是否想过您的 VBA 项目可能会给您的组织带来网络安全风险？
3.  Power Automate (以前的微软流程)是一个有影响力的工具，使员工能够使用无代码和低代码创建自动化流程，以提高效率。我们可以使用众多的[连接器](https://flow.microsoft.com/en-us/connectors/)来充分利用我们的数字生态系统。不幸的是，VBA 宏没有这些连接器。[你不认为通过微软团队](https://docs.microsoft.com/en-us/office/dev/scripts/resources/scenarios/task-reminders)发送一封自动电子邮件来提醒他们更新关于月末结算的状态会很酷吗？

![](img/77a2d7a7750956cdafdc69ec88d00f2d.png)

照片由[艾米丽·莫特](https://unsplash.com/@emilymorter?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## **Office 脚本不完善**

我们还必须承认，Office 脚本并没有涵盖所有功能，因为它仅限于 web 上的 Excel(尽管它带来了新的很酷的 web 场景)，而 VBA 的优势是可以利用桌面客户端上的所有功能。[事件过程](https://docs.microsoft.com/en-us/office/vba/excel/concepts/events-worksheetfunctions-shapes/using-events-with-excel-objects)无法使用 Office 脚本，我们必须手动运行/激活它们或通过 Power Automate 触发。此外，由于 VBA 已经存在了很长一段时间，通过学习一门新语言将其转换为 Office 脚本对许多组织来说可能很麻烦。

因此，我认为 Office 脚本不会很快占据主导地位。但是，通过手机上的应用程序在 ERP 系统上执行报告，将细节保存在 Excel 工作表中，然后在 Powerpoint 演示文稿中突出显示其要点，然后将其发送给利益相关者，这种想法令人兴奋。

我相信“分享就是关爱”的力量。请评论/分享您的反馈，并关注我的其他文章。

**参考文献:**

[](https://docs.microsoft.com/en-us/office/dev/scripts/) [## Office 脚本文档- Office 脚本

### 在 web 上使用 Excel 中的 Office 脚本来自动化您的常见任务。浏览以下文章，了解如何…

docs.microsoft.com](https://docs.microsoft.com/en-us/office/dev/scripts/) [](https://www.xelplus.com/excel-vba-vs-office-scripts/) [## Excel VBA vs Office 脚本- Xelplus - Leila Gharani

### Excel VBA 宏和 Office 脚本有什么区别？您需要从 VBA 切换到 Office 脚本吗…

www.xelplus.com](https://www.xelplus.com/excel-vba-vs-office-scripts/) [](https://flow.microsoft.com/en-us/) [## Power Automate | Microsoft Power 平台

### 使用 Microsoft Power Automate 简化重复性任务和无纸化流程，以便您可以将注意力集中在…

flow.microsoft.com](https://flow.microsoft.com/en-us/) 

**更多灵感:**