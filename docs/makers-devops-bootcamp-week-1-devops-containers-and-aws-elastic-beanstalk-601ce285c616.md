# 制造商 DevOps Bootcamp:第 1 周— DevOps、容器和 AWS 弹性豆茎

> 原文：<https://medium.com/codex/makers-devops-bootcamp-week-1-devops-containers-and-aws-elastic-beanstalk-601ce285c616?source=collection_archive---------5----------------------->

## [法典](http://medium.com/codex)

上周，我们结束了 Makers 软件开发学徒训练营的最后一周，并向我们的同事和雇主展示了我们的最终小组项目。过去的 12 周很紧张，但与一群了不起的人一起学习编程是一次令人难以置信的有益经历，我现在称他们为朋友。我们带着新获得的自信成长，表现得像个老板，并掌握了新的技能。

虽然还没有全部结束！对于我们中的一些人来说，我们还有六周的时间来完成制造商的第一个 DevOps 课程，我非常兴奋能够成为其中的一部分！

![](img/afdffbf3839fb95342819fab5af2e0ea.png)

由[罗曼·辛克维奇](https://unsplash.com/@synkevych?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

创客是一种自我导向的学习，DevOps 训练营的结构也不例外。该课程是为期六周的自学、研讨会和团队项目，课程涵盖 DevOps 最佳实践、集装箱化、无服务器、CI/CD、AWS、Azure、基础设施即代码以及站点可靠性工程中的常见问题。

我们通过学习 DevOps 是什么，为什么它在软件开发世界中如此重要，它是如何工作的，如何使用 Docker 封装一个 web 应用程序以及 AWS 的介绍开始了第一周的学习。

# DevOps 到底是什么？

对许多人来说，这是最近的流行语，但并不是每个人都真正知道它是什么，包括我，所以我非常兴奋地花第一天学习它到底是什么。

DevOps 并不局限于软件开发中的特定技术。在高层次上，它是一种文化，结合了新的实践、新的工具和新的思维方式，以弥合 it 运营、软件开发和质量保证之间的差距。它将敏捷软件开发实践和自动化结合到 It 运营的世界中。通过这种方法，DevOps 有助于缩短软件开发生命周期(SDLC ),使组织能够以安全可靠的方式更快地推出新产品和服务，同时提高运营绩效。

传统上，IT 运营和软件开发人员是分开工作的，但通过将这两个团队结合在一起，DevOps 工程师可以了解应用程序的整个生命周期，从规划阶段到开发和测试，到发布产品，到客户反馈以及基于该反馈的应用程序增强。

采用 DevOps 模型的一些主要优势:

*   **速度** —自动化是 DevOps 的支柱之一，意味着新功能和更新可以更快地发布，使企业能够更快地适应市场变化。
*   **快速软件交付** —随着新软件发布频率的增加，企业能够更频繁地迭代客户反馈，从而允许创新和改进产品发布。
*   **可靠性** —在应用程序发布前运行自动化测试，这增加了事情不出错的可能性，从而提高了它的可靠性。企业能够通过监控和日志记录实践实时了解应用程序的性能。
*   **扩展** —应用程序可以根据用户流量随时间的变化按需轻松扩展或缩减。
*   **改进的** **协作和沟通** —由于运营和开发部门的实践和工作流程相结合，团队合作得到了改善。随着团队成员在整个 SDLC 上的协作，整个团队有了更强的主人翁意识和任务共享，从而提高了他们向客户提供的服务质量。
*   **安全性** —这是 SDLC 的关键部分。现在每个人都在整个 SLDC 上工作，自动化的安全测试取代了手动测试，即使在大规模的情况下也更容易保持合规性。

# 容器——开发运维协作的基础

当我第一次听到我们正在学习集装箱时，我立即想到了海运集装箱。就其本质而言，与 DevOps 中容器的使用相比，它的用途并不太遥远。

![](img/4d6a49c58b8d1bcbe5ec0811cc54309e.png)

保罗·泰森在 [Unsplash](https://unsplash.com/s/photos/containers?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 什么是容器？

容器是独立的可执行包，包含运行应用程序所需的一切，包括相关的依赖项、库和配置文件。

那么 DevOps 和容器有什么关系呢？容器化应用程序为 DevOps 提供了显著的好处，包括通过可移植的轻量级容器管理和部署应用程序的灵活性(对于上面照片中的实际容器来说可能没有那么轻量级)。

它还使开发人员能够更容易地与操作和生产环境共享他们的应用程序，同时解决典型的日常“它在我的计算机上不起作用”问题，因为容器在不同的计算环境之间总是运行相同的。

# 码头工人

![](img/219769bf878073db8121b785b3f1d86b.png)

Docker 是容器技术领域的大玩家。这是 DevOps 中使用的一个流行的软件平台，允许您通过容器轻松地共享、管理和部署应用程序。

为简单起见，安装并运行 Docker 的计算机称为 Docker 主机或主机。

Docker 容器和 Docker 图像是使用 Docker 时需要了解的另外两个基本概念。

在主机上部署应用程序时，它会创建一个 Docker 容器。Docker 映像是带有构建容器说明的蓝图，其中包括应用程序及其相关依赖项。因此，Docker 容器是 Docker 映像的运行快照

# 将我们的第一个应用程序封装到云中

![](img/5f5af06a74d2bec3f308cd5a83c0014e.png)

克里斯蒂·马丁在 [Unsplash](https://unsplash.com/s/photos/cloud-computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

为了理解使用 Docker 的容器的好处，我们接受了第一周的第一个小组项目的任务，即在我们选择的任何东西上构建一个简单的 Node.js/Express 应用程序，然后使用 Docker ready for deployment on the cloud——AWS Elastic Beanstalk——对它进行容器化。我们有四天的时间来做这项工作，准备在周五向我们团队的其他人演示。

我的第一个想法是“天哪，我们真的要在第二天就投入进去了！”。然而，这给了我们一个真正的机会，将第一天的所有研讨会和阅读应用到实际项目中。

我们的四人团队决定构建一个简单的单页应用程序，因为我们想优先考虑我们的时间来学习 Docker，它管理容器的命令以及如何将我们的容器化应用程序部署到 AWS Elastic Beanstalk 上。

以下是我们的项目在接下来的四天里作为一个团队的进展情况:

**项目第一天(研究和应用构建):**

*   我们想要建立什么的集体头脑风暴会议；决定开发一个带聊天框的交互式绘图应用。
*   创建了我们团队本周的工作流程图。
*   学习如何为我们的应用程序设置一个基本的 Express 文件结构。
*   研究用于创建交互式画板和聊天框的 JavaScript 库。我们决定使用 [RoCanvas](https://re.trotoys.com/article/rocanvas/) 作为绘图工具，使用 [socket.io](http://socket.io) 作为聊天框。
*   设置我们的后端服务器，并创建我们的前端 HTML。

**项目第二天(将我们的应用装箱):**

*   通过使用 Docker 文件构建我们的 web 应用程序的 Docker 映像。AWS Elastic Beanstalk 也使用 Dockerfile 来部署应用程序。
*   学习了如何使用 Docker 在本地机器上运行 Docker 映像作为 Docker 容器。
*   将我们的 dockerised Node.js 应用程序提交并发布到 Docker Hub (Docker Hub 是一个存储库注册表，您可以在其中发布和下载容器映像—类似于源代码的 GitHub)。

**项目第 3 天(将 app 部署到 AWS Elastic Beanstalk):**

*   研究了 AWS 控制台界面和 Elastic Beanstalk 文档，以了解如何部署我们的容器化应用程序。
*   我们意识到有不同的方式来配置我们的 Docker 应用程序以部署到 AWS Elastic Beanstalk 上——您可以使用 Dockerfile、dockerrun.aws.json 文件，或者两者都使用！
*   通过使用 Dockerfile 和 dockerrun.aws.json 文件成功部署了我们的第一个容器化应用程序。

**项目第 4 天(最后润色和演示):**

*   前端的最终样式。
*   使用更新后的代码重新构建 Docker 映像，以重新部署到 AWS Elastic Beanstalk
*   演示时间和复古！

你可以在这里阅读更多关于我们团队项目[的信息。](https://github.com/cgloker/Hello-Cloud-World)

# 反射

我们介绍 DevOps、容器和 AWS 的第一周是一个陡峭的学习曲线，但在过去 12 周沉浸于开发方面的事情后，我非常喜欢学习更多关于工程的这个令人兴奋的部分。

在我们的回顾中，我们了解到，我们被分配完成第一周项目的方式是故意手动的，以便让我们第一次感受到使用容器和使用 AWS 的感觉。

我期待着下周继续讨论持续集成/持续交付/部署(CI/CD ),看看我们如何自动化我们在本周第一个应用程序的集成和部署阶段必须做的一些重复步骤。