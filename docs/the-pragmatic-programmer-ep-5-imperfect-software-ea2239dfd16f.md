# 实用程序员 EP.5 —不完美的软件

> 原文：<https://medium.com/codex/the-pragmatic-programmer-ep-5-imperfect-software-ea2239dfd16f?source=collection_archive---------15----------------------->

因为我们永远无法创建完美的软件。因此，我们采用以下技术来确保您将拥有的软件的高质量。

# 合同设计

![](img/bc03a7abaf4ff8a89bb5a23d88d63a12.png)

由 [Pixabay](https://www.pexels.com/@pixabay/) 以[像素](http://www.pexels.com)拍摄的照片

合同是两个或两个以上的人之间达成的协议，尤其是在商业交易中。其中一个是*客户*，他雇佣另一个被称为*供应商*的人去做一些事情。转到将契约应用到我们的代码上，在创建一个例程之前，我们必须声明包含三个主要部分的契约，正如 [Mayer](https://en.wikipedia.org/wiki/Bertrand_Meyer) 所描述的:

1.  前置条件:必须为真才能进入例程的条件。
2.  后置条件:进入例程后必须为真的条件。
3.  类不变式:从调用者的角度来看，必须始终为真的语句。

如果你答应打电话给我一个令人满意的先决条件，我会给你一个请后置条件的最终状态。

感谢在 [win.tue.nl](https://www.win.tue.nl/~wstomv/edu/2ip30/references/design-by-contract/index.html) 上提供以下示例的知识材料:

```
Obligations                        Benefits
--------------------------------------------------------------------
Client    Pay entire cost for one month in   Enjoy a low-cost
          advance. Bring only limited        hassle-free all 
          baggage and valid passports and    expenses paid vacation.
          visas, etc. Arrive at the airport
          for two hours before the flight 
          leaves.

Supplier  Locate, select and book airlines   Make profit on sale 
          carrier, auto rental, hotel, etc.  even if client does not 
          within required time frame and     show up at the airport 
          budget.                            on time and properly
                                             equipped. May even keep
                                             possible partial refund
                                             from hotel etc. in such
                                             cases.
```

下面是一个在 Eiffel 语言中应用 DbC 的示例:

感谢 [win.tue.nl](https://www.win.tue.nl/~wstomv/edu/2ip30/references/design-by-contract/index.html)

## 契约式设计(DbC)与测试驱动开发(TDD)

1.  DbC 需要嘲讽，而 TDD 需要。
2.  DbC 定义了所有成功和失败的案例，而 TDD 旨在测试一个特定的案例。
3.  TDD 只执行测试时间，而 DbC 是永远的，包括设计、开发、部署和维护阶段。

# 错误处理

每当错误出现时，它肯定是气味的事情发生了。正如上一集所写的，[务实的程序员 EP.4 — Tools](/codex/the-pragmatic-programmer-ep-4-tools-3bf554bccc7c) ，仔细阅读了那些错误信息。它想告诉我们一些事情。回到编码，我们可以使用这些技术产生更少的 bug。

## 提出所有可能的情况

如果您想要引发异常，您应该为所有可能发生的情况处理它们。否则，您应该在下面这样做，因为它不会误导真正的错误是什么，并且您不必在添加更多代码时修改异常处理部分。

## 提前冲突

为了避免可能发生的错误，在使用之前验证一些东西是常识。所以，试着尽可能快地把它们踢出去，而不是让它们一直流到最后。

# 断言

您可能不知道断言可以用在您的功能代码中，而不仅仅是测试代码中。是的，它可以用来明确地确保存储在变量中的值处于您所期望的状态。让我看看。

> 请记住，没有必要从您的产品中删除断言。

始终打开它们，因为它们有助于我们在使用前确保价值。众所周知，一部作品总是共享记忆和其他资源。这意味着完全内存分配很容易发生，而不是在您的个人笔记本电脑上执行。即使您面临由断言引起的性能问题，也不要关闭所有的断言，而是要视具体情况而定。

# 资源平衡

> 在同一个地方释放你所分配的

正如你在上面看到的代码块，你可能不认为这是正常的。但是，让我们仔细看看。它们在 3 个函数之间共享一个**文件**变量，并且与**文件**的连接从未关闭。

作为一个务实的程序员，你应该了解这些资源。你必须释放你已经分配的。为了更容易释放它，我们将**文件**保存在同一个地方，不将它暴露给另一个函数，就像这样:

如果您**需要**在不同的地方访问相同的资源，作者提供了两个建议

1.  尝试以与分配资源相反的顺序释放资源
2.  *尝试以相同的顺序分配资源，以避免[死锁](https://en.wikipedia.org/wiki/Deadlock)。*

*幸运的是，在面向对象的语言中，*垃圾收集器*通常可以在对象超出范围时自动释放，例如 Java。*

*在某些语言中，我们可以使用一个*变量作用域*来处理资源，例如 C++。同时，在许多语言中，除了还有一个**和一个**最后的**阶段。下面我们来看看:***

*请仔细用一个**最后的**子句写逻辑，让我们想象一下当 **open_file** 失败时会发生什么。当然，一个**文件**会在它还没有打开的地方被关闭。所以，应该是这样的:*

# *快速配速*

*![](img/11dd2d1197ed9a8853593e5936dd1701.png)*

*照片由[丹尼尔·雷切](https://www.pexels.com/@daniel-reche-718241/)以[像素](http://www.pexels.com)拍摄*

*作为一名工程师，我们通常相信我们所看到的，而不是神谕或难以预测的东西。因此，我们只是小步前进并获得反馈，例如，单元测试反馈(告诉你错过了哪一行)、安全性反馈、牛排架反馈、特性反馈等等。*

*请注意，上面显示的所有示例都是示例，只是为了强调对概念的理解。它可能被简化理解，而不是正确的语言语法。*

*感谢《实用编程》这本书提供了以上知识，如果你有兴趣阅读这本书，点击[此处](https://learning.oreilly.com/library/view/the-pragmatic-programmer/9780135956977/)了解更多详情。*