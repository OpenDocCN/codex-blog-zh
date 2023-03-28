# 尽早使用 Perl 语句修饰符

> 原文：<https://medium.com/codex/get-out-early-with-perl-statement-modifiers-1d4493d72dc5?source=collection_archive---------14----------------------->

![](img/d51dabb8153c69ad1416279ddd10e307.png)

*Photo by Pixabay 上*[T3【Pexels.com】](https://www.pexels.com/photo/arrow-communication-direction-display-235975/)

当我在 20 多岁的时候开始编写 Perl 时，我倾向于遵循我在学校通过 [Pascal](https://en.wikipedia.org/wiki/Pascal_(programming_language)) 学到的许多[结构化程序规则，尤其是每个函数都有一个](https://en.wikipedia.org/wiki/Structured_programming)[单一出口点](https://wiki.c2.com/?SingleFunctionExitPoint)的概念。例如:

```
sub double_even_number {
    # not using [signatures](https://perldoc.perl.org/perlsub#Signatures), this is mid-1990's code
    my $number = shift;
```