# 应该避免 Perl 中的 Yoda 条件

> 原文：<https://medium.com/codex/avoid-yoda-conditions-in-perl-you-should-e5daac8010d0?source=collection_archive---------9----------------------->

![](img/345147621e33e6ca973525872128edc8.png)

[*Paul VanDerWerf 来自美国缅因州布伦瑞克*](https://commons.wikimedia.org/wiki/File:Yoda_(29065553886).jpg)*[*CC BY 2.0*](https://creativecommons.org/licenses/by/2.0)*，via Wikimedia Commons**

*我记得在 2000 年代中期的一段短暂时间里，我在我的 Perl 中坚持所谓的[“Yoda conditions”](https://en.wikipedia.org/wiki/Yoda_conditions)。我会把常数放在相等的比较值左边。如果我不小心输入了一个单个的`=`而不是`==`，计算机会捕捉它，而不是轻松地[分配一个变量。例如:](https://perldoc.perl.org/perlop#Assignment-Operators)*

```
*if ( $foo == 42 ) { ... } # don’t do this
if ( 42 == $foo ) { ... } # do this
if ( $foo =…*
```