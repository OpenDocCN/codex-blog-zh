# 驯服驼鹿:选择子类化 Perl 方法的最佳方式

> 原文：<https://medium.com/codex/taming-the-moose-picking-the-best-way-to-subclass-perl-methods-e200a7bde662?source=collection_archive---------22----------------------->

![](img/074f046fcc15046da62d59eb2b2d7533.png)

Perl 的 [Moose](https://metacpan.org/pod/Moose) 对象系统中的`[override](https://metacpan.org/pod/Moose#override-($name,-&sub))`关键字是一个很好的代码文档，因为它明确声明了一个给定的方法覆盖了它的超类。它还有一个可以在`override`中使用的`[super](https://metacpan.org/pod/Moose#super)`关键字，称为“与原始方法具有相同参数的下一个最合适的超类方法”