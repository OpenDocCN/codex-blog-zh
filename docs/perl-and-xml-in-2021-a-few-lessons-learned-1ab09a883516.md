# 2021 年的 Perl 和 XML:一些经验教训

> 原文：<https://medium.com/codex/perl-and-xml-in-2021-a-few-lessons-learned-1ab09a883516?source=collection_archive---------15----------------------->

我已经有好几年没有研究过任何与 XML 相关的东西了，但是最近工作中的一个项目让我再次投入到对这个 90 年代的 mat 文档的生成、解析和修改中。如今，像 ly 这样的大多数开发人员只知道它是 web 浏览器中有趣的命名为[的 XML HTTPRe quest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 对象的一部分，用于从服务器检索 [JSON](https://en.wikipedia.org/wiki/JSON) for mat 中的数据，以及作为 [AJAX](https://en.wikipedia.org/wiki/Ajax_%28programming%29) 中的“X”。但是现在是 2021 年，仍然有大量的 API 和文档使用 XML 来完成工作。