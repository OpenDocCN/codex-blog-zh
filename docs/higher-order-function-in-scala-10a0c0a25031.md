# Scala 中的高阶函数

> 原文：<https://medium.com/codex/higher-order-function-in-scala-10a0c0a25031?source=collection_archive---------3----------------------->

![](img/ebb511788b25a8a0f2627d4cda16c4fd.png)

克里斯托弗·伯恩斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当谈到函数式编程时，高阶函数(HOFs)是一个非常热门的词，同时，当我们没有正确理解它时，它会变得有点混乱。我们将深入探讨 HOFs，scala 中的 currying 函数。

让我们看看 HOFs 的定义是什么:

```
Higher-order functions take other functions as parameters or return a function as a result. This is possible because functions are first-class values in Scala.
```

所以正如定义所说，我们可以看到一个例子，如何将一个函数作为参数传递，并对其求值。

现在这里的问题是把函数作为一个参数来传递现在，我们可以先看一下语法

```
 def function1 (<function_name> : <function_argument_type(s)> => <function_return_type>, ....) : return_type = { expressions} 
```

因此，如果我们可以看到上面的“function1”将一个函数作为参数，语法看起来会是什么样子:
让我们看看我们有一个发送问候消息的方法

```
def greet(name: String): String = s"Hello $name"
```

因此，上面的代码片段“greet”将只发送一个完整的问候消息给被传递的人。

现在让我们看看另一个方法，`message`,它可以接受参数名，也可以作为一个参数来问候这个人。

```
def message(fn: => String => String, name: String) : String = fn(name)
```

如果我们看到上面的“消息”函数，第一个参数是一个“fn ”(可以是任何占位符名称),是一个函数，后面跟有`:`和`=>`,下一条语句说明函数`fn`将接受什么参数，哪个是`String`,然后`=>`再次说明`fn`的返回类型，在我们的例子中，又是`String`。

到目前为止还不错，现在让我们来看看'问候';这是否满足内部“消息”功能的要求？答案当然是“有”。

太好了，那么让我们调用消息函数吧！！

```
val greetMsg = message(greet,"India")println(greetMsg)
```

输出:你好，印度

如果我们注意到，当把“greet”传递给`message`时，我们甚至没有提到括号。

## 现在我们将看看如何从另一个函数返回一个函数。

让我在这里创建两个函数:

```
def doubler(x: Int) = x * 2
def triplet(x: Int) = x * 3
```

现在我们将创建一个函数，它将上述函数作为返回类型发送:

```
def getOps(c:Int): Int => Int =  c match {
  case 2 => *doubler* case 3 =>  *triplet* case _ => throw new Exception("Not applicable number")
}
```

`getOps`此处的函数接受一个类型为`Int`的参数，并尝试根据条件检查它是否发送另一个方法，如果我们注意到`getOps`的返回类型为`Int => Int`，这意味着“ ***一个函数接受 Int 参数，并将结果作为 Int****“*发送，因此 doubler 和 triplet 符合条件。

这个怎么叫 BTW

```
def hofExamp(fn:Int => Int =>Int, x:Int):Int => Int = fn(x)
```

` fn: Int => Int =>Int `将 **Int** 作为参数，**返回另一个函数**，该函数将 **Int** 作为返回类型发送 **Int (Int = > Int)** 。够简单吗？！

最后一次呼叫 hofExamp:

```
val result: Int => Int =  *hofExamp*(*getOps*,3)
*println*(result(9)) res> 27 // because Triplet was the function return as value was 3
```