# 被认为有害的分支

> 原文：<https://medium.com/codex/branching-considered-harmful-ce20947d065a?source=collection_archive---------1----------------------->

*开发人员应该在主干上工作，使用 monorepo，并自动部署*

随着公司规模的增长，它倾向于在各种存储库和分支中积累源代码。我加入的一家初创公司有超过 80 个存储库——在时，他们雇佣的每个开发人员大约有[八个存储库。](https://medium.com/r?url=https%3A%2F%2Ftedspence.com%2Fcode-repository-size-with-powershell-4df93e5cdf88)

在这 80 个存储库中有多个分支:开发、QA、集成、部署、主控，等等。每个拥有项目的开发人员可以设计他们自己的分支策略。其中最复杂的保持六个不同的分支和樱桃…