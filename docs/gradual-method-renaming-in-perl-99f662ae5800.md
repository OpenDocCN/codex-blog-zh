# Perl 中的渐进方法重命名

> 原文：<https://medium.com/codex/gradual-method-renaming-in-perl-99f662ae5800?source=collection_archive---------12----------------------->

![](img/e725df992f8ca3c1127e4837b82b5510.png)

我们有超过 700，000 行 Perl 的庞大代码库，分布在[工作](https://www.hostgator.com/)的几十个 [Git](https://www.git-scm.com/) repos 里。有时[重构](https://www.refactoring.com/)很容易，如果所涉及的类和方法被定义为那些 repos 中的一个，但是上周我们想要重命名一个方法，这个方法可能在许多 repos 中被使用，而不需要 QA 和启动这么多的修改。在得到了丹书和瑞恩的帮助后…