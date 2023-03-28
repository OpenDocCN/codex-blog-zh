# 爪哇和番石榴未来终极指南

> 原文：<https://medium.com/codex/the-ultimate-guide-to-futures-in-java-and-guava-c53263dd69dc?source=collection_archive---------9----------------------->

## 解开未来的混乱

![](img/3c4874ff434254a1e33339a9d146b879.png)

照片由[德鲁·格雷厄姆](https://unsplash.com/@dizzyd718?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 Java 8 中引入了`CompletableFuture`,最终为 Java 带来了一种更好的处理异步逻辑的方法。与此同时，番石榴为这个问题创造了自己的解决方案。

现在，你应该使用哪一个？

首先，让我指出，番石榴有许多与期货交互的类，它们应该一起使用。所以大部分的课程不一定会互相影响。

但是首先，让我们看看普通的 Java 实现:

# 将来的

`[Future](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Future.html)`是基础接口，被 Java 自己的`CompletableFuture`和 Guava 的大部分接口和类使用。从 Java 5 开始就有了。

该接口非常简单，只提供了检查其状态、取消它或以阻塞方式(可选地带有超时)获得结果的方法。这适用于最简单的情况，但是如果您想同时处理多个`Future`或者甚至是链式操作，这个接口很快就会变得乏味。

摘自 java 文档的一些示例代码:

```
interface ArchiveSearcher { String search(String target); }
class App { ExecutorService executor = ...
    ArchiveSearcher searcher = ... void showSearch(final String) throws InterruptedException { Future<String> future
          = executor.submit(new Callable<String>() {
            public String call() {
                return searcher.search(target);
            }}); displayOtherThings(); // do other things while searching try {
            displayText(future.get()); // use future
        } catch (ExecutionException ex) { cleanup(); return; }
    }
}
```

# 可完成的未来

在 Java 8 中添加了`[CompletableFuture](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)`和它的接口`CompletableTask`，以允许更强大的期货操作。这两者紧密耦合，接口基本上可以忽略。

`CompletableFuture`实现了`Future`并提供了更多的方法来处理结果:

*   `thenAccept`允许您注册一个函数，该函数将在完成时被调用，其结果将用于从该方法返回的新的`CompletableFuture`。
*   `exceptionally`的功能与`thenAccept`相同，只是在`CompletableFuture`异常完成时被调用。
*   `handle`允许您在同一个函数中处理成功值和异常。
*   `join`将阻塞，直到`CompletableFuture`完成，或者返回值或者抛出任何发生的异常。

它还提供了一些创建`CompletableFutures`的便捷方法:

*   `supplyAsync`让我们指定一个将被异步调用的供应商，一旦完成，其结果将是`CompletableFuture`的结果。
*   `completedFuture`将立即返回一个成功完成的未来。它的对等物`failedFuture`是在 Java 9 中添加的。

一些示例代码:

```
public CompletableFuture<List<User>> loadActiveUsers() { return httpClient.<List<User>>sendAsync(request, bodyHandler)
        .thenApply(this::filterListOfUsersForActiveOnes)
        .exceptionally(throwable -> {
            LOG.warn(throwable);
            return List.of();
       });
}public void displayListOfActiveUsers() { try {
        CompletableFuture<> activeUsersFuture = loadActiveUsers();
        displayOtherStuff();

        try {
            displayActiveUsers(activeUsersFuture.join());
        } catch (CompletionException e) {
            LOG.error(e);

            displayNoUsers();
        }
    }
}
```

## `CompletableFuture`的问题

虽然`CompletableFuture`流畅的风格无疑是对`Future`简单方法的巨大改进，但它仍然有其怪癖。

首先，人们很容易忘记它也可以被取消。这意味着如果你回复一个`CompletableFuture` **呼叫者可以取消它！**这可能会导致资源没有被正确关闭或出现意外状态。避免这种情况的最简单方法是用另一个包裹你的`CompletableFuture`。

另一个问题是异常的处理有点不一致，文档中没有提到:

```
NullPointerException exception = new NullPointerException(":(");CompletableFuture.failedFuture(exception)
    .exceptionally(throwable -> {
        // here throwable is the exception from above
        return null;
    });CompletableFuture.failedFuture(exception)
    .thenApply(Object::toString)
    .exceptionally(throwable -> {
        // here throwable is a CompletionException that wraps the
        //  exception from above
        return null;
    });
```

最后，只处理特定的异常并不容易。`exceptionally`中的函数接收到一个`Throwable`的实例，这是一个被检查的异常。这意味着如果不先用`RuntimeException`包起来，最好是用`CompletionException`包起来，你就不能再扔了。

当这些方法中的一个抛出的`CompletionException`被直接返回时(例如当使用`join`时)，嵌套的方法不会被解包。这意味着您很可能会得到类似这样的样板代码:

```
.exceptionally(throwable -> {
    while (throwable instanceof CompletionException) {
        throwable = throwable.getCause();
    } if (throwable instanceof NullPointerException) {
        // handle the exception I care about
        return null;
    } else {
        throw new CompletionException(throwable);
    }
})
```

# 期货

离开 Java 标准库，Guava 的静态类`[Futures](https://guava.dev/releases/snapshot/api/docs/com/google/common/util/concurrent/Futures.html)`包含了更有效地处理未来的方法。这主要是通过利用我们接下来要看的`ListenableFuture`接口来实现的。

然而，值得注意的是，它还有一个`transform`方法，允许你基于另一个方法和一个函数创建一个新的未来。新的未来将以所提供的未来被给定的功能转换的结果来完成。这本质上模仿了`CompletableFuture.thenApply`的工作方式。

这堂课应该是你尝试期货和番石榴的第一站！

# ListenableFuture

`[ListenableFuture](https://guava.dev/releases/snapshot/api/docs/com/google/common/util/concurrent/ListenableFuture.html)`是 Guava 中实现`Future`的一个简单接口，只增加了一个方法:`addListener`。

此方法允许您注册一旦将来完成就调用的侦听器。尽管它们不直接得到结果，但是您可以使用`ListenableFuture`本身的`get`方法得到结果。

这个接口是在`Futures`类中使用的主要接口。也是大部分 Guava 未来的类实现的。

## ListenableFutureTask

[这个类](https://guava.dev/releases/snapshot/api/docs/com/google/common/util/concurrent/ListenableFutureTask.html)本质上是`ListenableFuture`接口的一个基本实现。它有两个使用`Callable`或`Runnable`的工厂方法，但除此之外只实现其接口的方法。

## 可确定的未来

`[SettableFuture](https://guava.dev/releases/snapshot/api/docs/com/google/common/util/concurrent/SettableFuture.html)`用 setter 方法扩展了`ListenableFuture`接口的方法。你可以设置它的成功值，例外甚至基于另一个未来来设置。

这个类主要用于当你已经有一个线程，你想完成你的未来。

## 未来

顾名思义，`[AbstractFuture](https://guava.dev/releases/snapshot/api/docs/com/google/common/util/concurrent/AbstractFuture.html)`是`ListenableFuture`接口的抽象实现。除了已经提到的`addListener`和 setters，它还有一些公开其内部状态的方法。

你不太可能需要这个类，但是它可以用来实现你自己版本的`ListenableFuture`。

# 流动未来

`FluentFuture`与 Java 的`CompletableFuture`最为相似。它实现了`ListenableFuture`，还提供了`transform`和`catching`中的映射方法。

在这种情况下，`catching`允许捕捉特定的异常，而不仅仅是`Throwable`。这使得异常处理的方法更具选择性，理论上检查异常的可能性不会导致大量样板代码。

一些来自其文档的示例代码:

```
ListenableFuture<Boolean> adminIsLoggedIn =
     FluentFuture.from(usersDatabase.getAdminUser())
         .transform(User::getId, directExecutor())
         .transform(ActivityService::isLoggedIn, threadPool)
         .catching(RpcException.class, e -> false, directExecutor());
```

# 关闭未来

`[ClosingFuture](https://guava.dev/releases/snapshot/api/docs/com/google/common/util/concurrent/ClosingFuture.html)`类似于`FluentFuture`，但更多的是作为一个建设者，而不是未来本身。它提供了转换值、捕捉异常和组合多个异常的方法。最后，一个`ClosingFuture`总是要被“完成”。

目前有两种方法可以完成它:

*   `finishToFuture`将返回一个`FluentFuture`,一旦`ClosingFuture`定义的所有步骤完成，它将完成。
*   `finishToValueAndCloser`完成后将调用回调。

其文档中的一个示例:

使用 ClosingFuture 的示例代码摘自其文档

# 结论

当使用普通 Java 时，你最好的选择是`CompletableFuture`。一旦你也有番石榴，你有无数的选择。它们都填补了略有不同的空白，并意味着不同层次的细节。

最后，芭乐的很多课可以很快变得让人应接不暇，一头雾水。

就个人而言，我可能会多尝试一下`ClosingFuture`和`FluentFuture`，但是现在坚持使用`CompletableFuture`——主要是因为我可以安全地将它放在 API 中，而不必担心引入新的依赖项。