# Perl 中的多种继承方式

> 原文：<https://medium.com/codex/multiple-ways-to-inheritance-in-perl-346ec9736ea6?source=collection_archive---------6----------------------->

![](img/57144d14b1a45170a679f436d1ca8e70.png)

受我在周末看到的 par ents 的启发，我想我应该写一下 Perl 类也可以有“par ents ”,它们从 parents 中继承了它的方法。虽然表面上看起来似乎有不止一种方法可以实现，但是这些技术都共享相同的底层机制。

# 一切都在那里`[BEGIN](https://perldoc.perl.org/perlmod#BEGIN%2C-UNITCHECK%2C-CHECK%2C-INIT-and-END)` s: `[@ISA](https://perldoc.perl.org/variables/@ISA)`