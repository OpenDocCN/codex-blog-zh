# 关于 Golang 的 Defer，你需要了解什么？

> 原文：<https://medium.com/codex/what-do-you-need-to-know-about-golangs-defer-4fac71e0f00b?source=collection_archive---------2----------------------->

![](img/d25c1ba6e7fbd36beed7f969683d8d94.png)

Golang 是一种函数式编程语言。它是一种强大而灵活的语言编程，为开发人员提供了从零开始设计程序架构的完整范围，并且具有很高的自由度。这种语言有其独特之处。因此，Golang 有一些其他语言可能没有的独特功能。其中之一就是**延期函数**。

# 什么是围棋延时？

Go 的 defer 是一个预定的函数调用，将在函数生命周期结束时运行。简单来说就是一个清理函数调用。您可以设置一个命名函数或构造一个匿名函数，如下面的[示例](https://play.golang.org/p/nw2RTVO3H4B)所示。对于清理任务来说，这是一个非常有用的功能，清理任务可能包括关闭连接、取消订阅事件、资源清理，甚至从死机中恢复。

```
**func** main() {
  **defer** foo()
  **defer** func() {
    fmt.Println("hello")
  }()
}**func** foo() {
    fmt.Println("foo")
}
```

延迟机制本身很简单，但如果使用得当，它会非常强大。当调用 defer 语句时，它将函数调用推入堆栈列表，并使用 LIFO 原则。因此，如果一个函数声明了多重延迟，最后一个被推的延迟将被首先调用，如下面的[示例](https://play.golang.org/p/5BBRZyrz-p1)所示。

```
**for** i := 0; i < 5; i++ {
    **defer** fmt.Printf("%d ", i)
}
```

输出:

```
4 3 2 1 0
```

关于 Go 的 defer 机制还有很多其他的东西，比如关于 defer 的三个基本规则，与[**recover**](https://golang.org/doc/effective_go#recover)**和 [**panic**](https://golang.org/doc/effective_go#panic) 一起工作。更详细的 golang 机制可以在 [golang 博客](https://blog.golang.org/defer-panic-and-recover)上找到。你也可以在 Golang 文档中阅读更多关于延期的信息**

# **有用的提示**

**在本文中，我们将探索和讨论 defer 能做和不能做的许多事情，作为开发人员需要做或避免做的事情，defer 的限制和功能。记住，这些案例大多是我亲身经历的。这里写的任何建议，纯属我的看法。因此，它可能无法直接回答你的问题，但我希望分享我在这些技巧上的研究和经验可能有助于更深入地理解延迟。**

# **零函数**

**当你试图推迟一个可变函数。始终确保变量函数不为零。否则，它将在延迟执行时死机。你可以在 Golang [游乐场](https://play.golang.org/p/pmIGnjFhOpz)玩耍。**

```
**var** cleanUp **func**()**func** main() {
   **defer** cleanUp()
   fmt.Println("hello")
}
```

**但是，这是一种不好的做法，不建议将函数用作变量。尽可能避免使用函数变量，考虑另一种方法，如私有函数。它不仅确保了非零功能场景，而且还增强了 Golang 中[关注点分离](https://en.wikipedia.org/wiki/Separation_of_concerns)设计原则的良好实践。**

```
**func** main() {
   **defer** cleanUp()
   fmt.Println("hello")
}**func** cleanUp() {
   fmt.Println("foo")
}
```

# **注意秩序**

**正如在[博客 Golang](https://blog.golang.org/defer-panic-and-recover) 上所述，defer 的基本规则之一如下:**

> **在周围的函数返回后，延迟的函数调用按后进先出的顺序执行。**

**这条规则简单地说明了延迟函数只对 LIFO 顺序的函数返回执行。使用延迟功能时，请始终牢记这条规则。有几个与这条规则相关的常见错误，你可以记下来。**

## ****小心使用 for 循环****

**在循环中使用 defer 函数时要小心，除非你知道自己在做什么。请记住，for 循环不是一个函数，defer 可能不会像您预期的那样工作，如下面的[示例](https://play.golang.org/p/QGzh8QMQR2L)所示。**

```
**func** main() {
 **for** i := 0; i < 3; i++ {
   **var** num **int**
   **defer** **func**() {
      fmt.Println("Defer", num)
   }()
   num = Double(i)
 }
}**func** Double(num **int**) **int** {
  result := num * 2
  fmt.Println("Inner", result)
  **return** result
}
```

**输出:**

```
Inner 0
Inner 2
Inner 4
Defer 4
Defer 2
Defer 0
```

**在这个例子中，**延迟函数** **直到主函数**结束才被执行。这可能意味着对关键执行的严重后果，例如在循环内延迟关闭连接。此外，**由于 LIFO 堆栈原理，defer 函数调用的顺序是颠倒的**。**

**我们可以用几种方法解决这个问题。**

**1.在循环内部创建一个函数，将每个循环的执行封装为一个整体。它确保每次循环都执行 defer 函数。**

```
**for** i := 0; i < 3; i++ {
  **func**() {
    **var** num **int**
    **defer** **func**() {
      fmt.Println("Defer", num)
    }()
    num = Double(i)
  }()
}
```

**2.直接在现场调用就可以了，根本不用 defer。这是最简单的解决方法。它可能不需要对 defer 做任何事情，但这可能是在 Golang 中使用 for loop 的最佳实践。**

```
**for** i := 0; i < 3; i++ {
  num := Double(i)
  fmt.Println("Defer", num)
}
```

## ****后进先出顺序可能比较棘手****

**虽然一旦理解了 LIFO 堆栈原理，延迟执行就很简单了。如果你随意放置它们，仍然可能会使代码的意义变得混乱。因此，**延迟函数通常写在需要清理的原始函数之后**。下面是一个利用延迟函数的糟糕代码的例子。**

**您可能会意识到这个例子的一些问题。**

*   **首先，如果`db.Begin()`遇到一个会导致资源泄漏的错误，`db.Close()`不会执行。**
*   **第二，这里的`err`变量到处都在使用，并且也覆盖了其他延迟函数，这可能会混淆下一个使用`err`变量进行验证的延迟函数。**

**下面是一个使用延迟函数的良好实践代码示例。**

**作为一名开发人员，您可能会立即意识到哪些代码更容易阅读并且有意义。这对你来说似乎微不足道，但它将帮助很多人，减少人为错误。**

## ****在使用 defer** 之前先知道正确的顺序**

****注意您将要使用的功能**。阅读函数文档，如果需要的话直接深入到代码中。确保您对函数在每种情况下返回的内容有信心。之后，你就可以知道延迟函数需要以什么顺序执行。这是一个可能的错误顺序的例子。**

```
rows, err := tx.Query("SELECT ...")
**defer** rows.Close()
**if** err != nil {
   **return** err
}
```

**err 不为零且 rows 为零时的输出:**

```
**panic**: runtime error: invalid memory address or nil pointer dereference
```

# **延迟参数范围很复杂**

## ****我的参数讨厌范围****

**Golang 变量的作用域很简单。在外部作用域上定义的变量可以被每个内部作用域访问。但是，一旦内部作用域在相同的名称下定义了新变量。它将在内部作用域上创建一个新变量，引用一个不同于外部作用域的全新变量。**

**随着[命名返回值](https://golang.org/doc/effective_go#named-results)的进入，这变得更加复杂。在这种情况下，变量在函数生命周期开始时立即被实例化，可以作为常规变量使用。独特的是，您可以使用`return`而不使用任何局部变量，它将使用函数的命名变量的当前值。**

**命名返回值的复杂性是 Golang 上一些常见错误的根本原因。其中之一是在使用延迟函数时。下面是一个例子。**

```
**func** execute() (err **error**){
   db, err := sql.Open()
   **if** err != nil {
      log.Fatal(err)
   }
   **defer** **func**(){
      **if** err := db.Close(); err != nil {
         ...
      }
   }
   ...
   **return**
}
```

**我们期望一旦`db.Close()`出错，该函数将从关闭 db 返回一个错误。但是，在这段代码中情况并非如此。因为`db.Close()`在一个延迟函数中，并且它还用`:=`实例化了一个新的 err 变量，该变量不会覆盖来自命名返回`(err error)`的错误。解决方案就是直接在延迟函数中使用指定的返回变量，因为函数返回值带有 err。**

```
**defer** **func**(){
   if err = db.Close(); err != nil {
     ...
   }
}
```

## ****在运行中评估****

**基于[博客 Golang](https://blog.golang.org/defer-panic-and-recover) 的围棋延期规则之二是**

> ***对 defer 语句求值时，会对延迟函数的参数求值。***

**也许，在这种情况下，一个例子本身会比我试图解释它更有效。[游乐场](https://play.golang.org/p/JYnLI16knWO)**

```
**func** a() {
    i := 0
    **defer** fmt.Println(i)
    i++
    fmt.Println(i)
    **return**
}
```

**输出:**

```
1
0
```

**总之，任何作为参数传递给延迟函数的东西都将被立即计算。那么延迟函数将使用评估后的参数，即使原始参数现在可能已经有了不同的值。**不要将这与直接使用变量** **而没有任何参数**的延迟匿名函数混淆。在这种情况下，它指向同一个变量。通常，开发人员会忽略这种类型的细节，并导致他们犯下 defer 中最常见的错误之一。**

**假设我们有一个钱包类型。[游乐场](https://play.golang.org/p/h6G6U6HmYpn)**

**输出:**

```
$ 1000
$ 500
$ 500
```

**我们试着把`myWallet`作为参数传递怎么样。[游乐场](https://play.golang.org/p/AS0HYEyyx_T)**

```
**func** main() {
   myWallet := Wallet{1000}
   myWallet.MyMoney() myWallet.AddMoney(100)
   **defer** **func**(myWallet Wallet) {
      myWallet.MyMoney()
   }(myWallet) myWallet.DeductMoney(600)
   **defer** myWallet.MyMoney()
}
```

**输出:**

```
$ 1000
$ 500
$ 1100
```

**然后试着把我的钱包换成指针`myWallet := &Wallet{1000}`。你认为会发生什么？答案可在*延迟方法接收器*下的后续章节中找到。继续读！**

## ****再次循环…****

**正如前面在*谨慎使用循环中提到的，*在循环中使用 defer 会变得相当棘手。现在利用*求值参数*和*参数范围*，在一个循环中延迟是非常麻烦的。例如，当您想要在匿名函数中打印一个索引循环时。[游乐场](https://play.golang.org/p/FHHPc13eSD9)**

```
**for** i := 0; i < 2; i++ {
   **defer** **func**() {
      fmt.Println(i)
   }()
   fmt.Println(i)
}
```

**输出:**

```
0
1
2
2
```

**所发生的是延迟函数参数在循环结束后指向相同的变量状态。如果延迟函数想要在每个循环中使用变量状态，那么延迟函数需要在每个循环中计算参数。有多种方法可以实现这一点。这可以通过使用带参数的匿名函数来完成。**

```
**for** i := 0; i < 2; i++ {
   **defer** **func**(i int) {
      fmt.Println(i)
   }(i)
   fmt.Println(i)
}
```

**或者直接使用延迟功能:**

```
**for** i := 0; i < 2; i++ {
   **defer** fmt.Println(i)
   fmt.Println(i)
}
```

**输出:**

```
0
1
1
0
```

# **被遗忘的回归**

**你知道 Go 的延迟既不返回值也不返回错误吗？即使延迟函数返回一些东西，它也不会影响任何东西。然而，作为第三条规则，延迟函数可以分配和更改返回命名值，如[博客 Golang](https://blog.golang.org/defer-panic-and-recover) 中所述。**

> ***延迟函数可以读取并赋值给返回函数的指定返回值。***

## **错误在哪里？**

**开发人员经常马上使用 defer，因为它太方便了，他们忘记了 Golang 语言中最重要的东西。这是错误处理。与其他可以捕捉父函数错误的编程语言不同，Golang 依赖于对返回值的错误处理。通常，一些 IDE 如 *Visual Basic* 和 *GoLand* 提供了一个功能来警告开发者未处理的错误。但是，作为一名优秀的 Golang 开发人员，我们需要养成更好地处理错误的习惯，而不是总是依赖警告信号。**

```
**defer** db.Close()
```

**更好的错误处理:**

```
**defer** **func** () {
   **if** err := db.Close(); err != nil {
      log.Println(err)
   }
}()
```

## ****遗漏退货****

**如果 Go 的 defer 不处理延迟函数返回的值，那么这些值在代码中的最终位置是哪里？它消失了，形象地说，变成了原子。它只是不拾取或引用任何变量，最终被垃圾收集器收集。**

**那么问题来了，如果需要的话，它的父函数的延迟返回值是怎样的？解决这个问题的一个方法是使用前面提到的命名返回值[。defer 函数只需要将值赋给命名的返回参数，父函数将使用这些参数作为最终的返回值。](https://golang.org/doc/effective_go#named-results)**

```
**func** execute(tx *sql.Tx) (err **error**){
   **defer** **func** () {
      **if** err != nil {
         **if** errRb := tx.Rollback(); errRb != nil {
            err = errRb
            **return**
         }
      }
   }()
   …
   **return**
}
```

**然而，请记住，Go 的 defer 的目的首先是鼓励在函数式编程中使用干净的代码。因此，**当函数仍然能够正常返回值的时候，你不需要每次都使用 defer**。Defer 是为了帮助您完成一项清理任务，这项任务在函数每次返回后都必须运行。但是，它不是返回非必要值的工具。比如像`tx.Commit()`。**

# **延迟方法接收器**

**到目前为止，我们主要讨论了延迟属性和机制。现在，我们可以开始用指针和[方法接收器](https://tour.golang.org/methods/1)来增加趣味。Golang 方法也可以与 defer 和 compliment 一起使用，但有一个转折。我们将使用在运行部分评估的*中使用的相同示例。让我们改为改变方法的指针。[游乐场](https://play.golang.org/p/VJNUVy8AtqH)***

```
**func** (wallet Wallet) MyMoney() {
   fmt.Println("$", wallet.money)
}...**func** main() {
   myWallet := Wallet{1000} myWallet.AddMoney(100)
   **defer** myWallet.MyMoney() myWallet.DeductMoney(600)
   **defer** myWallet.MyMoney()
}
```

**输出:**

```
$ 500
$ 1100
```

**当`MyMoney`使用方法接收器上的指针时进行比较:**

```
**func** (wallet *Wallet) MyMoney() {
   fmt.Println("$", wallet.money)
}
```

**输出:**

```
$ 500
$ 500
```

**这里发生了什么？当在延迟函数上注册/计算时，不带指针的方法打印带有值状态的结果值。另一方面，带有指针的方法打印反映在最后更新的值上的两个结果。**

**它通过立即计算参数与延迟的工作方式有关。更多详情，请参考*运行*部分的评估。当使用 defer 时，将立即计算接收方，并将其复制到 defer 以供以后执行。如果接收者 *(Wallet)* 没有指针，那么复制到接收者的钱包和它的钱的状态是从它在那个时间*(在这个例子中是 1100)* 注册时开始的。**

**它同样适用于指针方法接收器 *(*Wallet)* 。但是，在这种情况下，复制到 defer 中的接收器是指针的副本，该指针仍然指向与当前接收器相同的地址。因此，在 defer receiver 中的钱的值仍然跟随主函数中发生的所有变化，直到结束。**

## **使用匿名函数**

**把戏并没有就此结束。还有更多。从在运行中评估的*的例子中我们知道，我们使用匿名函数`func(){}()`立即评估`myWallet`。[游乐场](https://play.golang.org/p/AS0HYEyyx_T)***

```
**defer** **func**(myWallet Wallet) {
   myWallet.MyMoney()
}(myWallet)
```

**试着玩玩吧。你会遇到一个新问题。为什么即使我改变了方法接收器指针，结果总是一样？是因为`myWallet`当场就被评价为延期函数 param。意味着为延迟函数创建了`myWallet`变量的第二个实例。因此，对原始`myWallet`的任何改变都不会改变`myWallet`的第二个变量，因为它有不同的内存地址。如果我们能在正常执行上表示它，它会是这样的:**

```
**func** main() {
   myWallet := Wallet{1000} myWallet.AddMoney(100)
   myWallet2 := myWallet // Do something deferredFunc(myWallet2)
}**func** deferredFunc(myWallet Wallet) {
   myWallet.MyMoney()
}
```

**如果您仍然感到困惑，您可以使用 IDE 调试器来查看`MyMoney`函数中`Wallet`上的每个内存地址。 [Git gist](https://gist.github.com/kben19/f58cd7d349a79d54ae21c7878cf0b81b) 。**

## **指针异常**

**假设您有一个在指针变量上使用方法接收器指针的复杂用例。是的，刚开始其实很困惑，但是你会习惯的。我们先来看一个例子。**

```
**func** (wallet *Wallet) MyMoney() {
   fmt.Println("$", wallet.money)
}**func** main() {
   myWallet := &Wallet{1000} myWallet.AddMoney(100)
   **defer** myWallet.MyMoney() myWallet.DeductMoney(600)
   **defer** myWallet.MyMoney()
}
```

**输出:**

```
$ 500
$ 500
```

**让我们用普通方法接收机来试验一下。**

```
**func** (wallet Wallet) MyMoney() {
   fmt.Println("$", wallet.money)
}**func** main() {
   myWallet := &Wallet{1000} myWallet.AddMoney(100)
   **defer** myWallet.MyMoney() myWallet.DeductMoney(600)
   **defer** myWallet.MyMoney()
}
```

**输出:**

```
$ 500
$ 1100
```

**结果显示了答案。就像我们之前讨论过的，当一个函数的接收者不是指针时，那么接收者复制的值只是一个结构的状态。由于接收器不是指针，所以无论`myWallet`是否是指针，该值在延迟注册时已经固定。**

**下表总结了使用方法 receiver 的这一部分。**

 **[## 延期方法接收方摘要- Google Drive

### 编辑描述

docs.google.com](https://docs.google.com/spreadsheets/d/e/2PACX-1vSfwsxy-1lBrXmcrswjeGgzBk3cWoQDnD1Vkmxg7Ir1frb85vifY-nM2o7Zv-2Vi5JQyhjhInC53Ft3/pubhtml?gid=0&single=true)** 

# **救援中的恢复**

**Golang 有自己的方法来产生意外的运行时错误，这在正常情况下通常不应该发生。叫做[慌](https://gobyexample.com/panic)。如您所见，Golang 将 error 视为需要返回的值。不像其他语言有自己的异常处理错误并捕捉它。因此，处理死机可能会很麻烦，因为如果处理不当，它可能会停止程序。在 golang 上处理恐慌的一种方法是使用恢复内置函数。**

**Recover 函数的目的是从死机错误中重新获得控制。除了处理死机之外，它还捕获死机给出的值，并将其作为响应值返回。你可以在[博客 golang](https://blog.golang.org/defer-panic-and-recover) 上看到关于`recover`的更多信息。**

**不幸的是，根据我的观察，许多开发者并不知道`recover`功能。这可能与`recover`需要被放置的性质有关，因为它通常被写在包装器或像 *main* 这样的最顶层父函数中。这也是为什么我们看不到`recover`在程序中发挥很多作用的原因之一。因此，我将尽力解释如何正确使用 recover。**

## **恢复和延迟是一对**

**没错，题目就是这个意思。**没有延期，恢复是完全无用的**。他们需要在一起工作。他们注定要在一起。让我们试着把它们分开，看看会发生什么。(*我为他们感到难过***

```
**func** main() {
   **recover**()
   **panic**(“panicking”)
}
```

**输出:恐慌仍在肆虐。**

```
**panic**: panicking
```

**下面是一个如何正确使用 recover 的示例。**

```
**func** main() {
   **defer** **func**() {
      recoveredValue := **recover**()
      fmt.Println("recovered value:", recoveredValue)
   }()
   **panic**("panicking")
}
```

**输出:**

```
recovered value: panicking
```

## **恢复值**

**现在我们知道了如何正确使用`recover`函数。下一步是利用从恐慌中恢复的值。大多数情况下，开发人员用一个字符串或一个错误来初始化 panic。但是，根据 panic golang [文档](https://pkg.go.dev/builtin#panic)，panic 参数实际上是一个接口。它也适用于基于[文档](https://pkg.go.dev/builtin#recover)的恢复函数返回值类型。**

```
**func** panic(v **interface**{})**func** recover() **interface**{}
```

**基于这些文档，**我们可以在技术上把任何有用的东西分配到恐慌中，并在稍后的**恢复。开发人员可以比以前更多地分析恐慌，或者将它用于其他用例。这为我们在程序中如何处理和使用恐慌提供了充分的灵活性。例如，让我们用定制的错误来尝试一下。**

**假设我们有一个定制的一般错误。[游乐场](https://play.golang.org/p/oKXNI6nSHX-)**

**输出:**

```
recovered: error 500
```

# **结论**

**Golang defer 函数对于编写干净的代码来说是非常有用和必要的。基于我们所看到和讨论的，Golang defer 很容易使用和理解，但很难掌握。在学习了 Golang 的延迟机制及其良好的实践后，我们可以用一种新的方式来看待延迟，并准备充分利用延迟的潜力。我希望这篇文章能帮助你深入了解延迟。感谢您到目前为止阅读我的文章。**