# 数据从业者是新的(安全)最薄弱环节吗？

> 原文：<https://medium.com/codex/do-data-practitioners-are-the-new-security-weakest-link-9880ec91de63?source=collection_archive---------7----------------------->

![](img/32702f6109c5e696952199cb4eed8adc.png)

照片由[前方什么也没有](https://www.pexels.com/@ian-panelo?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)来自 [Pexels](https://www.pexels.com/photo/closed-old-gates-in-nature-7425339/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

# **密码中的秘密**

代码现象中的秘密是当今软件世界面临的主要威胁之一。从[优步 2016 年的数据泄露](https://money.cnn.com/2017/11/22/technology/uber-hack-consequences-cover-up/index.html)到最近的[太阳风的违规](https://savebreach.com/solarwinds-credentials-exposure-led-to-us-government-fireye-breach/#:~:text=Vinoth%20confirmed%20that%20the%20FTP,the%20US%20Government%20breach%20that)，黑客正在利用无意中遗留在代码中的秘密轻松侵入组织。这就是为什么 [DevSecOps](https://www.redhat.com/en/topics/devops/what-is-devsecops#:~:text=DevSecOps%20stands%20for%20development%2C%20security,throughout%20the%20entire%20IT%20lifecycle.) 正在[左移](https://www.cloudsavvyit.com/13123/what-does-it-mean-to-shift-security-left/)，更加关注开发早期阶段，确保秘密始终安全可靠，不会留在代码中。但是，虽然重点放在了组织的主要存储库上，但员工的帐户却被放在了一边，实际上成为了新的安全弱点。为什么监视员工的存储库如此重要？

# **源代码托管服务——明确的社交网络**

许多人倾向于忘记，像 GitHub 和 GitLab 这样的服务不仅仅是一个源代码托管和协作的地方，而且首先是社交网络。虽然用户可以更新他们的个人资料，并关注存储库和帐户，但他们也可以将代码上传到他们的私人帐户，同样，许多人往往会忘记这是公开共享的。问题是开发人员[会忘记代码中的秘密(Sinha 等人)](https://ieeexplore.ieee.org/document/7180102)，这意味着至少一些发布到员工个人(公开共享)Github 账户的代码会包含他们的秘密。如何防止黑客窃取那些机密？如何及时捕捉和缓解？。根据梅利等人的说法，一旦秘密泄露给 Github，人们只需[20 秒](https://www.ndss-symposium.org/ndss-paper/how-bad-can-it-git-characterizing-secret-leakage-in-public-github-repositories/)就能找到秘密。对于容忍员工开源的公司来说，这种缓解变得更加复杂。以拥有数千名员工的[Meta](https://www.quora.com/How-many-software-engineers-work-at-Facebook#:~:text=of%20Technology%20(2021)-,Originally%20Answered%3A%20How%20many%20developers%20work%20at%20Facebook%3F,developers%20running%20the%20Facebook%20Inc.)为例；即使他们会监视他们中的每一个人，即使他们会在秘密泄露的那一刻发现它，谁知道它是公司还是私人的呢？此外，私人秘密还存在吗？(窃取员工的私人谷歌凭据可能足以最终获取他们的工作相关机密。尤其是在疫情期间，每个人都在家工作并远程进入他们的企业服务)。更不用说承包商和用户的私人账户(不是公司提供的账户，而是他们自己开的账户)，雇主可能都不知道。当我们考虑不同的开发人员范围时，问题变得更加复杂，也就是说，让我们更深入地看看数据从业人员。

# **数据从业者** **代码气味**

数据员工和 R&D 组织的其他员工之间的主要区别之一是，大多数数据员工可能不是根据他们的代码开发技能招聘和评估的(即使他们有，这也从来不是主要的 KPI)，这意味着假设他们的代码实践是较低的标准是公平的。因此，当开发人员普遍倾向于忘记他们在代码中的秘密时，这种现象将会进一步出现在这个领域也是有道理的。鉴于数据员工通常会与组织数据进行交互——这增加了泄漏变得严重的几率(例如，考虑一下[优步案例](https://money.cnn.com/2017/11/22/technology/uber-hack-consequences-cover-up/index.html))，并鉴于数据员工生态系统通常包括许多外部服务(无论是在[谷歌实验室](https://colab.research.google.com/)上训练模型，在[亚马逊 S3](https://aws.amazon.com/s3/) 上保存数据集，还是在 [Tableau](https://www.tableau.com/) 仪表盘上查看分析)——这增加了代码中被遗忘的潜在秘密的数量。为了进一步展示它，我们做了一个小测试，在 Github 中搜索 Python(通常用于数据应用程序，特别是在数据科学家、机器学习工程师和数据员工等数据从业者中)代码，该代码连接一个流行的云[数据湖](https://en.wikipedia.org/wiki/Data_lake)服务。考虑到这些资产的重要性(应该包括一个组织拥有的几乎每一个数据片段)，我们假设相关的片段会格外小心地处理凭证，使用像[环境变量](https://en.wikipedia.org/wiki/Environment_variable)这样的最佳实践。但是现实是完全不同的。我们发现了许多里面有明显秘密的文件。**有些似乎与组织资产明显相关**。等待邪恶的黑客来利用它们。

# **问题范围**

我们描述的问题并不是数据员工独有的。公平地说，在许多情况下，他们的代码存在于组织后台最黑暗的地方，使得潜在的攻击者不太容易看到。但与此同时，一旦这样的泄漏发生，问题范围急剧变得严重；虽然客户端开发人员(例如)通常会接触一些公司内部服务，但数据员工通常每天都要与组织数据的最关键部分进行交互。这并不意味着只有数据从业者的工作应该被额外验证；严重的泄密可能发生在组织的每一个员工身上。主要区别在于，这种泄漏可能与组织数据等超级关键资源有关。鉴于这个问题可能在许多地方发生，这个问题变得更加严重；不仅仅是在组织或员工资料库上，简单的搜索就能发现几乎所有与源代码相关的社交服务上都有明显的秘密，无论是在 [StackOverflow](https://stackoverflow.com/) 问答、 [Docker hub](https://hub.docker.com/) 图片、 [Google play](https://play.google.com/) 应用，还是在私人博客和论坛上，使用一些简单的 [Google dorks](https://en.wikipedia.org/wiki/Google_hacking) 就能很容易地找到。

# **如何避免？**

一如既往，第一步是更好的教育；确保您的员工了解最新的安全最佳实践(如**不要在代码**中使用普通机密 **和**永远不要在公共账户上使用**)。它应该被验证，尤其是在那些不太面向代码的数据组中(可能就像存在于那里的许多数据组)。同时，值得注意的是，错误会发生。在公司帐户上，我们可以应用现有的解决方案来验证存储库不包含机密。但由于公司机密可能会出现在网络上的其他地方，一个更全面的解决方案是尝试监控潜在的泄露区域，在其中搜索机密，一旦发现，尝试评估其风险。问题是这不是一个简单的要求，而是一个覆盖所有可能泄漏区域的持续努力。中间且更可行的解决方案是理解**每个接触代码的人都应该被验证**具有安全基本理解和最佳实践。不管他们的角色是什么。没有它，你的秘密被黑只是时间问题。**