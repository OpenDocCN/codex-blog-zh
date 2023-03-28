# 最被低估的科特林函数

> 原文：<https://medium.com/codex/the-most-underrated-kotlin-function-2655459e9e4a?source=collection_archive---------1----------------------->

## Kotlin 中运算符函数的介绍。

![](img/4b59f4a9764fbbba23093784f50c8a64.png)

克林特·帕特森在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

与 Java 相比，Kotlin 中有许多类型的函数，我说的是*许多* — 。我们有**扩展**函数、**中缀**函数、**内联**函数、**高阶**函数、 **tailrec** 函数，最后是**运算符**函数。许多开发人员使用扩展函数和高阶函数，但是没有多少人理解**运算符**函数的用法

# 理解运算符功能

**什么是运营商？这是一个关键字，用于执行某种运算，如算术、逻辑和关系运算。**

**什么是函数？**它是一个可以随时调用的可执行代码块。

**那么什么是算子函数呢？**是由某个*操作符*关键字*调用*的一段可执行代码。它们必须在类或顶级扩展函数中。

# 运算符函数的一个基本示例

运算符函数必须有一个接收器类。换句话说，它们必须在类或顶级扩展函数内部。下面我们来基本看一下下面的代码:

第 10 行是奇迹发生的地方。`times`是一个特定的操作符函数，调用时使用星号(*)。receiver 类是一个`String`，它只允许一个参数`n: Int`，对我们来说，就是重复的次数。

默认情况下，你**不能**使用代码`sampleString * 5`并期望它工作。您需要为此定义一个操作函数。

# 操作员功能的实际能力

让我们从创建一个矩阵类开始，定义一些初始化矩阵的函数和属性。

我已经记录了这些函数，这样更容易理解。`rows`是类的实际矩阵；它是一个二维双数组。

## get(Int)运算符函数

我想用矩阵类像 2D 数组一样用*超能力*！例如，如果我想访问 Matrix 类的第一行，通常我必须获取属性。但这不再是必需的了。

```
val matrix = matrix {
  row(3, 5) // Kotlin-DSL function to add a row
  row(-2, 1)
}/* usually */
println(matrix.rows[0][2])/* using get operator function */
println(matrix[0][2])
```

立刻，我们需要我们的代码变得更可读。`matrix`后的第一个`[]`调用`n`为 0 的`get(n)`函数。

## 正(矩阵)算子函数

有一些函数和构造函数，我没有展示它们的实现，但这确实会影响理解。

在`plus()`函数中，我们接受一个参数，这个参数就是一个`Matrix`本身。我们调用`plus()`操作符并返回第三个`Matrix`。

```
val A = matrix {
  row(8, -5)
  row(3, -1)
}// invoking the plus() function with the operator
val C = A + matrix {
  row(4, 5)
  row(0.3, -3.0)
}
```

这是 GitHub 资源库的链接。

[](https://github.com/cybercoder-naj/KotlinAI/) [## GitHub - cybercoder-naj/KotlinAI:用 Kotlin DSL 结构做一个关于线性代数的程序…

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/cybercoder-naj/KotlinAI/) 

我希望你喜欢读我的文章。谢谢大家！✌️

```
**Want to connect?**[My GitHub Profile](https://github.com/cybercoder-naj)
[My LinkedIn Page](https://www.linkedin.com/in/nishant-a-jalan)
```