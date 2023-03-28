# 你已经知道单子了

> 原文：<https://medium.com/codex/you-already-know-monads-2065f6309971?source=collection_archive---------7----------------------->

我不会解释单子背后的抽象理论，我只会向您展示一些您已经理解的 Java 代码。使用这种实用的方法，你会意识到你很可能已经使用过几次单子了。

哦，如果你更喜欢听我说，这篇文章是基于我的一个简短演讲，我试图在 7 分钟内解释清楚。

# 单子是什么？

单子只是实现以下两个方法的类:

*   `[of](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#of-T...-)`(在解释中通常称为`unit`或`return`)和
*   `[flatMap](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#flatMap-java.util.function.Function-)`(通常称为`bind`)

`of`应该简单地接受一些值并返回一个包装器(单子)。`flatMap`然后允许通过对所有这些值应用一个函数来访问这些值，获取这些函数返回的单子，并再次将它们组合(也称为“展平”)成一个。

利用这一点，最简单的单子如下:

```
public class Monad<T> {
    private T value; private Monad(T value) {
        this.value = value;
    } public static <T> Monad<T> of(T value) {
        return new Monad<>(value);
    } public<R> Monad<R> flatMap(Function<T, Monad<R>> f){
        return f.apply(value);
    }
}
```

## 就这样吗？

大部分是这样，但是很明显你不能用一种特定的方式命名这些方法，并且有一个单子。这两种方法需要如何协同工作是有规则的。

好消息是，如果你像上面描述的那样实现了这些方法(正如他们通常理解的那样)，你就已经实现了所有这些方法。但不管怎样，它们都在这里:

1.  `of`方法是`flatMap`的**左标识**，
2.  `of`方法是`flatMap`的**权限标识**
3.  `flatMap`方法是**关联**。

或者，在代码中:

```
// using
Monad<V> m;
V x;
Function<V, Monad<V>> f;
Function<V, Monad<V>> g;// rule 1: of is left-identity of flatMap
of(x).flatMap(f) .equals(f.apply(x))// rule 2
m.flatMap(Monad::of) .equals(m)// rule 3
m.flatMap(f).flatMap(h)
    .equals(m.flatMap(v -> g.apply(v).flatmap(h)))
```

# 我为什么要在乎？

现在你知道了单子是什么，以及如何创建单子，但是为什么它还有一个名字呢？

如果你使用的是一种功能强大的语言，那么这甚至不是一个真正的问题，因为你在进行功能性编程时会遇到的工具和问题很可能会自然地导致你创建一些单子。单子最初就是从那里来的。

也就是说，它们在这些语言之外也非常有用，事实上许多新的 API 都有单子的特性。

## 在您的域中包含(错误)状态

最好的 API 试图阻止你犯错。实现这一点的方法之一是在其返回值中显式包含错误状态。当一行中的多个函数调用可能返回错误时，这还有一个额外的好处，就是使结果代码更漂亮。

让我们看一个使用 Optional 的例子:

```
public Optional<Integer> doSomething(Stream<Integer> stream) {
    return stream
        .findFirst()
        .flatMap(this::integerHalf)
        .flatMap(v -> Optional.of(v*5));
}public Optional<Integer> integerHalf(Integer i) {
    if (i%2 == 0) {
        return Optional.of(i/2);
    } else {
        return Optional.empty();
    }
}
```

在这个例子中，我们不仅可以选择在任何时候返回一个“错误”,而且之后我们不必立即检查它。相反，这可以在稍后的点*完成。*

单子也可以用来处理非错误状态。这经常在集合或异步编程中使用(见`[CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)`)，在那里它也可以防止回调。

## 允许使用更通用的函数

以上面的函数`integerHalf`为例:我们可以对普通整数使用这个函数，也可以对任何单子使用这个函数，比如使用`flatMap`方法的`Optional`或`Stream`。

这种在不离开 Monad 域的情况下访问底层信息的便利性可以创建惊人的 API，防止许多错误。

# 你知道一些单子吗？

我已经提到了 Java 中三种最常见的单子:

*   `Optional`，
*   `Stream`和
*   `CompletableFuture`

但我相信你已经遇到更多了。如果你知道另一个好例子，请发表评论。

# 更多链接

*   [可能是单子(以及函子和应用程序)背后理论的最佳解释——附图片！](https://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)
*   [很好地解释了为什么函数式编程时单子会自然出现](https://stackoverflow.com/questions/28139259/why-do-we-need-monads/28139260#28139260)
*   [我的 7 分钟闪电谈这个话题](https://www.youtube.com/watch?v=w0fhQCzy2g0&t=1308s)

![](img/70471ed6638347b7b09e1215481ff337.png)

单子的精确图像(摄影:[妮可·德·霍尔斯](https://burst.shopify.com/@ndekhors)