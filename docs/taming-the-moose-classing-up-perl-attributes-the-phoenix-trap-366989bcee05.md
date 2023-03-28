# 驯服麋鹿:对 Perl 属性进行分类

> 原文：<https://medium.com/codex/taming-the-moose-classing-up-perl-attributes-the-phoenix-trap-366989bcee05?source=collection_archive---------6----------------------->

![](img/1c1f4fdef8e551b5cd1171ea30515c98.png)

在[我的工作](https://www.hostgator.com/)中，我们广泛使用 [Moose](https://metacpan.org/pod/Moose) 对象系统来处理那些可能会非常繁琐的模板[面向对象的 Perl 代码](https://perldoc.perl.org/perlobj)。在代码库的一个部分，我们有一个[类](https://metacpan.org/pod/Moose::Manual::Classes)族，它将 [Perl 方法](https://perldoc.perl.org/perlobj#A-Method-is-Simply-a-Subroutine)映射到我们大型组织中第三方 API 中各种调用的名称。这些私有的 Perl 方法依次从公共的