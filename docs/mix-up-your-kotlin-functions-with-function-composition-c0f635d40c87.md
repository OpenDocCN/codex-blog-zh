# 将 Kotlin 函数与函数组合混合使用

> 原文：<https://medium.com/codex/mix-up-your-kotlin-functions-with-function-composition-c0f635d40c87?source=collection_archive---------0----------------------->

## Kotlin 和 Haskell 之间的函数式编程交换

最近，我学习了一种不同的函数式编程语言，叫做 Haskell。让我印象深刻的是`.`操作符，它是一个二元操作符，接受两个函数并输出另一个函数。这个`.`操作符在像 Haskell 这样的语言中非常有用，我想知道我是否可以在 Kotlin 中复制它。

![](img/b1948704d256af4dfb7f055c968ef855.png)

亚历克斯·康德拉蒂耶夫在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# (。Haskell 中的)运算符

在 Haskell 中，`.`用于组合两个函数。这是函数定义:

```
(.) :: (b -> c) -> (a -> b) -> a -> c
(.) f g x = f (g x) 
```

如果你不认识哈斯克尔，请不要担心。这里我定义了一个函数，它接收一个泛型类型的函数`b`并输出`c`，另一个函数接收`a`并返回`b`。给出这两个函数，它返回另一个函数，该函数接受`a`并返回`c`。

## Haskell 中的用法

我们可以把 map 函数看作是这个主题的一个主要例子。

```
composition :: [Int] -> [Double]
composition = map ((10.5 -) . (* 100.0))

main = do
  let list = [1..10]
  print (composition list)
```

与任何其他语言一样，map 函数接受列表和转换列表中每个元素的函数。在这里，我们先将**的**和*乘以 100* 和**然后再将**和*的数字从 10.5* 中减去。

# 科特林函数合成

我在 Kotlin 中创建了一个中缀扩展函数，如下所示:

```
infix fun <A, B, C> ((A) -> B).then(other: (B) -> C): (A) -> C {
    return { other(this(it)) }
}
```

我们来分解一下这个函数要做什么。

1.  我们用三种通用类型定义函数`then`:`A`、`B`和`C`。
2.  它是 lambda 函数的扩展:`(A) -> B`。
3.  它接受一个类型为`(B) -> C`的参数。
4.  它返回一个类型为`(A) -> C`的 lambda 函数。
5.  给函数加上前缀`infix`。

## 科特林语的用法

用法与 Haskell 不太相似，因为它不是本地的；然而，这是对代码的一个很好的补充。

```
fun main() {
  val f1: (Int) -> Double = { it * 100.0 }
  val f2: (Double) -> Double = { 10.5 - it }

  val range = 1..10
  println(range.map(f1 then f2))
}
```

# 结论

要不要把 OOP 概念从 Java 翻译过来？没问题。你希望融入 Haskell 的函数式语言概念吗？太棒了。Kotlin 作为一种语言的灵活性令人惊叹，这使它成为一种优秀的语言(在我看来)，也是我个人最喜欢的语言。

我希望你喜欢读我的文章，并学到了一些东西。谢谢大家！✌️

```
**Want to connect?**

My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```