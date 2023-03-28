# 没有 Makefile 时的 Perl 测试覆盖率

> 原文：<https://medium.com/codex/perl-test-coverage-when-you-dont-have-a-makefile-d19db387ff6e?source=collection_archive---------4----------------------->

![](img/4d697f8d5e5a7aaa9a449e9ed78680bb.png)

[是的，第三天的配对程序](https://perlmaven.com/course-management-app-in-mojolicious-4)有 [Gábor Szabó](https://szabgab.com/) 我反复思考了一会儿，试图找出如何为应用程序获取[测试覆盖范围](https://en.wikipedia.org/wiki/Code_coverage)统计数据。文档列出了如何以几种方式运行模块，但没有准确描述如何单独运行模块，而不是运行阿宁 T2 的测试。我今天想出了怎么做，并且用[和](https://troglodyne.net/) …