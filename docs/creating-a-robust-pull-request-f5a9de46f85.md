# 创建可靠的拉取请求

> 原文：<https://medium.com/codex/creating-a-robust-pull-request-f5a9de46f85?source=collection_archive---------22----------------------->

![](img/e055d127933df22d9e958900f011bd08.png)

安妮·斯普拉特在 [Unsplash](https://unsplash.com/s/photos/programming-team?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

我过去不喜欢审核流程，因为它会减慢我的 PRs 被合并并部署到生产中的速度。我记得我当时的首席技术官 [Arzumy MD](https://medium.com/u/2f2c757229ad?source=post_page-----f5a9de46f85--------------------------------) 在一次谈话中谈到了这个问题，他指导我理解了为什么我们必须在我们创建的 PRs 中付出更多努力。我记得的一些要点是:

*   PRs 在全球范围内对每个人开放(在技术团队中)，所以你可以阅读其他高级工程师的 PRs，看看他们如何以及为什么写哪一行代码。
*   你的公关也将提供给每个人，尽可能的自我记录，这样它可以为你自己和你的同事提供更多的信息，以备不时之需。
*   你应该能够使用你的 PR 来帮助你自己(现在和将来)和你的同事，他们将审查你的代码，提供尽可能多的你所做工作的背景。

这样的小进步对我的技术职业生涯产生了巨大的影响，我如何看待代码/拉取请求，因为我将我的 PRs 视为一个产品本身，我有责任确保它“如其所愿”地工作，并且它具有可观察性，以验证它与过去和未来的 PRs 一起工作(输入规格，但我们将在其他时间讨论)。

因此，我将解释并详细说明我个人是如何处理任何人分配给我的每张票的。这都是我个人的观点，所以它可能对你有用，也可能对你没用，我把判断留给你和你的公司如何进行软件工程过程。我们主要关注公关方面，而不是代码。

# **提交**

我坚信不要为公关创造太多承诺。通常，信念是创建尽可能多的提交，但是这使得回溯代码变得非常困难，并且`git blame`/git lens 对回溯没有帮助。我使用`[git reflog](https://www.atlassian.com/git/tutorials/rewriting-history/git-reflog)`来检查我的进度，并将一个主要特性限制在一次提交中。我也经常使用`git rebase -i`来管理我的提交，所以你可以提交所有你想提交的，然后再压缩它。如果需要更多的提交，我总是用票号标记提交的首字母。例如，`MANGO-245`，其中`MANGO`是你的团队/jira 董事会的名称，`245`是门票号码。

*   帮助在提交历史记录中跟踪提交。
*   当像 datadog 或 sentry.io 这样的服务追溯异常和错误时，您可以准确地指出问题在哪一行以及在哪一个 PR 中被合并和部署。
*   一旦有了包含这类信息的最小提交消息，就有助于调试哪里出错了。
*   有助于修复补丁，因为如果需要的话，你可以从一个分支中挑选出一个完整的特性，或者删除它🤷‍♂.

**git 中的分支**是另一个谜。它可以简单复杂。困难之处在于从哪里分支以及指向哪里。在这方面需要特别小心。大多数情况下，最好是使用 git 的某种工具，如 [sourcetree](https://www.sourcetreeapp.com/) ，但我使用命令行和基于 gui 的解决方案的组合方法←这只是在事情非常复杂的情况下，也就是说，您的整个公司使用一个单一的模式文件，并且您只希望您的更改显示在您的 PR 中。这让我想到:

# **Git 部分**

这是最关键的部分之一，但我们对此习以为常，以至于不去想它。我想指出我通常的流程，然后是一些边缘案例。

*新功能请求*

```
$ git checkout develop  //assuming a 'main'/'develop' approach
$ git pull              // to pull all the latest changes
$ git checkout -b "feature/MANGO-245-describe-the-ticket"
-- do moi werk --
$ git commit -m "MANGO-245 describe the commit"
$ git push origin head
```

如果我需要做一个不太大的更改，我不会创建另一个提交…在`-- do moi werk again --`之后，我会:

```
$ git pull origin <target-branch> --rebase
$ git commit --amend --no-edit
```

`--no-edit`标志让您拥有相同的提交消息。
我恳请您查看 git 命令，如:

```
$ git reflog
$ git rebase -i
$ git reset (--hard | --soft)
```

这些都是我从日常角度能想到的。对于任何 git f*** up，git 有一个惊人的博客，你可能想看看。

当你在逆境中挥舞你的 git 力量时，请保持检查你的 PR 目标分支的进展。

使用`$ git diff feature/MANGO-245-your-ticket...develop`，检查不断增加的差异，众所周知，这些差异有时每秒钟都会增加。

评论你的心流，我会把它添加到博客上。或者我们可以用 git-flows IDK 创建一个存储库，我很乐意接受这个想法😃。

现在，我们几乎完成了逻辑和 git 的工作，我们必须考虑如果我们的公关造成公司大规模停机，我们会因此损失数十亿美元，世界上每个人都会死亡……**只有 cz 的公关**。*啜饮咖啡*可能不会，但你知道，你永远不知道…无论如何

# **拉式请求模板**

我喜欢一个好的公关模板，让公关成为一种享受。除了**保持 PR 尽可能小**应该是一个高优先级的事实之外，PR 模板确实可以帮助评审者了解已经完成了什么样的工作。由于上下文切换是最难完成的任务之一，我们必须用足够的上下文来帮助我们的评审者，这样就不会增加他们的认知复杂性。

[](https://betterprogramming.pub/github-templates-the-smarter-way-to-formalize-pull-requests-among-development-teams-89f8d6a204f) [## Github 模板:在开发团队中形式化拉取请求的更聪明的方法

### 不再有令人困惑的拉取请求

better 编程. pub](https://betterprogramming.pub/github-templates-the-smarter-way-to-formalize-pull-requests-among-development-teams-89f8d6a204f) 

链接一篇发表在[https://betterprogramming.pub/](https://betterprogramming.pub/)的非常好的文章。

主要的目标是总是问你自己，我们如何能通过帮助一些背景来帮助减少我们非人道努力的人性方面的一些紧张。

旁注:这些详细的公关模板可以帮助你解释你做了什么改变以及为什么(Psst…归咎于`MANGO-245`，经典😉).

总而言之，这是我个人在每一次公关中做的几件事。我真诚地希望它能帮助你更有效地管理你的工作，并为你节省一些焦虑，因为我们在各自的盘子里都有足够的，因为它是…