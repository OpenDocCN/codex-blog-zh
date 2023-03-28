# 在 Julia 中使用多重分派

> 原文：<https://medium.com/codex/using-multiple-dispatch-in-julia-c09b5f38b4d9?source=collection_archive---------8----------------------->

## 文章节选

## *摘自*[*Julia for Data Analysis*](https://www.manning.com/books/julia-for-data-analysis?utm_source=medium&utm_medium=referral&utm_campaign=book_kaminski2_julia_3_17_22)*作者 Bogumil Kaminski*

![](img/2c8866375242cd3eb9ada63b90426467.png)

*这篇文章向你展示了如何在 Julia 中使用多重分派。*

如果你是一名数据科学家或任何处理大量数据的人，或者如果你对 Julia 语言感兴趣，请阅读这本书。

通过在[manning.com](https://www.manning.com/books/julia-for-data-analysis?utm_source=medium&utm_medium=referral&utm_campaign=book_kaminski2_julia_3_17_22)的结账处将 **fcckaminski** 输入折扣代码框，为数据分析 Julia 打八五折。

让我们学习如何定义具有不同方法的函数，并将这些知识应用到一个名为`winsorized_mean`的函数中。

## 为函数定义方法的规则

幸运的是，如果你理解了 Julia 类型系统的工作原理，定义方法就相对容易了。您只需将类型限制添加到`::`之后的函数的参数中。如果省略了类型说明部分，那么 Julia 假设允许使用`Any`类型的值。

假设我们想创建一个函数`fun`,它采用一个位置参数，行为如下:

*   如果传递的是一个数字，它应该打印`"a number was passed"`，除非它是一个 Float64 类型的值，在这种情况下，我们希望打印`"a Float64 value"`；
*   在所有其他情况下，我们希望打印`"unsupported type"`。

下面是一个例子，说明如何通过为函数`fun`定义三个方法来实现这种行为。

```
julia> fun(x) = println("unsupported type")
 fun (generic function with 1 method)

 julia> fun(x::Number) = println("a number was passed")
 fun (generic function with 2 methods)

 julia> fun(x::Float64) = println("a Float64 value")
 fun (generic function with 3 methods)

 julia> methods(fun)
 # 3 methods for generic function "fun":
 [1] fun(x::Float64) in Main at REPL[3]:1
 [2] fun(x::Number) in Main at REPL[2]:1
 [3] fun(x) in Main at REPL[1]:1

 julia> fun("hello!")
 unsupported type

 julia> fun(1)
 a number was passed

 julia> fun(1.0)
 a Float64 value
```

在上面的例子中，注意到例如`1`是一个`Number`(因为它是`Int`)但它不是`Float64`，所以最具体的匹配方法是`fun(x::Number)`。

## 方法模糊问题

在为一个函数定义多个方法时，您必须记住的是避免方法模糊。当 Julia 编译器不能决定应该为给定的一组参数选择哪种方法时，就会发生这种情况。举个例子最容易理解这个问题。假设您想要定义一个带有两个位置参数的 bar 函数。它会告诉你其中是否有数字。这是实现这种功能的第一次尝试:

```
julia> bar(x, y) = "no numbers passed"
 foo (generic function with 1 method)

 julia> bar(x::Number, y) = "first argument is a number"
 foo (generic function with 2 methods)

 julia> bar(x, y::Number) = "second argument is a number"
 foo (generic function with 3 methods)

 julia> bar("hello", "world")
 "no numbers passed"

 julia> bar(1, "world")
 "first argument is a number"

 julia> bar("hello", 2)
 "second argument is a number"

 julia> bar(1, 2)
 ERROR: MethodError: foo(::Int64, ::Int64) is ambiguous. Candidates:
   bar(x::Number, y) in Main at REPL[2]:1
   bar(x, y::Number) in Main at REPL[3]:1
 Possible fix, define
   bar(::Number, ::Number)
```

正如你所看到的，在我们想要通过传递一个数字作为第一个和第二个参数来调用`bar`之前，一切都运行得很好。在这种情况下，Julia 抱怨说，它不知道应该调用哪个方法，因为有两个方法可以选择。幸运的是，我们得到了如何解决这种情况的提示。我们需要定义一个额外的方法来解决这种不确定性:

```
julia> bar(x::Number, y::Number) = "both arguments are numbers"
 foo (generic function with 4 methods)

 julia> bar(1, 2)
 "both arguments are numbers"

 julia> methods(bar)
 # 4 methods for generic function "foo":
 [1] bar(x::Number, y::Number) in Main at REPL[8]:1
 [2] bar(x::Number, y) in Main at REPL[2]:1
 [3] bar(x, y::Number) in Main at REPL[3]:1
 [4] bar(x, y) in Main at REPL[1]:1
```

## **为什么多次派遣有用？**

理解 Julia 中的方法是如何工作的，这是你应该掌握的知识的重要部分。正如你在上面的例子中看到的，它允许用户根据函数的任何位置参数的类型来区分函数的行为。结合灵活的类型层次系统，多重分派允许 Julia 程序员编写高度灵活和可重用的代码。请注意，通过在适当的抽象级别指定类型，用户不必考虑将传递给函数的每一种可能的具体类型，同时仍然保留对接受何种值的控制。例如，如果您定义了自己的`Number`子类型，例如，decimals . JL([https://github.com/JuliaMath/Decimals.jl](https://github.com/JuliaMath/Decimals.jl))包提供了支持任意精度十进制浮点计算的类型，您不必重写代码。即使原始代码不是专门针对这个用例开发的，所有代码都可以使用新的类型。

## 改进了 winsorized mean 的实现

我们准备改进我们的`winsorized_mean`函数定义。下面是您实现它的方法:

```
julia> function winsorized_mean(x::AbstractVector, k::Integer)
            k >= 0 || throw(ArgumentError("k must be non-negative"))
            length(x) > 2 * k || throw(ArgumentError("k is too large"))
            y = sort!(collect(x))
            for i in 1:k
                y[i] = y[k + 1]
                y[end - i + 1] = y[end - k]
            end
            return sum(y) / length(y)
        end
 winsorized_mean (generic function with 1 method)
```

首先注意，我们已经限制了`x`和`k`的允许类型，因此如果您尝试调用该函数，它的参数必须匹配所需的类型:

```
julia> winsorized_mean([8, 3, 1, 5, 7], 1)
 5.0

 julia> winsorized_mean(1:10, 2)
 5.5

 julia> winsorized_mean(1:10, "a")
 ERROR: MethodError: no method matching winsorized_mean(::UnitRange{Int64}, ::String)
 Closest candidates are:
   winsorized_mean(::AbstractVector{T} where T, ::Integer) at REPL[6]:1

 julia> winsorized_mean(10, 1)
 ERROR: MethodError: no method matching winsorized_mean(::Int64, ::Int64)
 Closest candidates are:
   winsorized_mean(::AbstractVector{T} where T, ::Integer) at REPL[6]:1
```

此外，我们可以在代码中看到一些使它变得健壮的东西。首先，我们检查传递的参数是否一致，也就是说，如果`k`是负的或者太大，那么它就是无效的，在这种情况下，我们通过调用以`ArgumentError`作为参数的`throw`函数来抛出一个错误。看看如果我们通过错误的`k`会发生什么:

```
julia> winsorized_mean(1:10, -1)
 ERROR: ArgumentError: k must be non-negative

 julia> winsorized_mean(1:10, 5)
 ERROR: ArgumentError: k is too large
```

接下来，在排序之前，复制一份存储在`x`向量中的数据。为了实现这一点，我们使用了`collect`函数，该函数接受任何可迭代的集合并返回一个存储相同值的对象，该对象具有`Vector`类型。我们将这个向量传递给`sort!`函数，对其进行就地排序。

你可能会问为什么需要使用`collect`函数来分配一个新的`Vector`。原因是，例如像`1:10`这样的范围是只读的，因此以后我们将无法用`y[i] = y[k + 1]`和`y[end - i + 1] = y[end - k]`更新`y`。此外，一般来说，Julia 可以支持数组中非基于 1 的索引(参见[https://github.com/JuliaArrays/OffsetArrays.jl](https://github.com/JuliaArrays/OffsetArrays.jl))。但是，`Vector`使用基于 1 的索引。总之，使用`collect`函数可以将任何集合或通用`AbstractVector`转换成 Julia 中定义的标准`Vector`类型，这种类型是可变的，并且使用基于 1 的索引。

最后注意，我们没有手动执行 for 循环，而是使用了更简单、更健壮的`sum`函数。

## **在方法中加入参数类型注释是否提高了方法的执行速度？**

向函数参数添加类型注释使得 Julia 代码更容易阅读，也更安全。用户经常问的一个自然问题是，它是否提高了代码执行速度。

如果某个函数只有一个方法，那么添加类型注释并不能提高代码执行速度。原因是，当调用某个函数时，Julia 编译器知道您传递给它的参数的类型，并使用这些信息生成本机代码。换句话说:类型限制信息不影响代码生成。

但是，如果为某个函数定义了多个方法，情况就不同了。原因是类型限制会影响方法调度。然后，使用针对给定类型的值优化的算法，每个方法可以有不同的实现。使用多重分派允许 Julia 编译器选择最适合您的数据的实现。

我来举例说明一下。考虑我们在第 2 章中介绍的`sort`函数。通过调用`methods(sort)`，您可以了解到它在基本 Julia 中定义了五种不同的方法(如果您加载了一些 Julia 包，可能会更多)。有一种对带有签名`sort(v::AbstractVector; kws...)`的向量进行排序的通用方法和一种对带有签名`sort(r::AbstractUnitRange)`的 1:3 范围进行排序的专用方法。

拥有这种专门的方法有什么好处？答案是第二种方法被定义为`sort(r::AbstractUnitRange) = r`。因为我们知道类型`AbstractUnitRange`的对象已经排序(它们是增量等于 1 的值的范围),所以我们可以返回传递的值。在这种情况下，利用方法签名中的类型限制可以显著提高`sort`操作的性能。

目前就这些。感谢阅读。