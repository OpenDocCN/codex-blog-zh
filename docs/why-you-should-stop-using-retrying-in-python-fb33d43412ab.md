# 为什么应该停止在 python 中使用重试

> 原文：<https://medium.com/codex/why-you-should-stop-using-retrying-in-python-fb33d43412ab?source=collection_archive---------8----------------------->

## 不再维护重试。用韧性代替。

![](img/5323427dd99d457d2c9a58b90ca4d8ef.png)

照片由[亨利&公司](https://unsplash.com/@hngstrm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 简介:

如果你是一个 web 开发人员，你应该知道服务器错误有多烦人，或者任何与此相关的错误。想象一下，有一大块下载/上传文件的代码，最后在第 n 分钟抛出了一个`exception`。不幸的是，这种事经常发生。相信我，伙计，我也经历过。

作为一名开发人员，我们不仅应该提供没有错误的代码(让我把它改写成错误最少的代码😉)，但也是一个优雅的失败，一个信息性的错误消息，也许还有一个重试机制。

python 中有多个重试库，我们现在用它们来实现指数回退。有几个是，

很遗憾，不再支持重试。我们从重试中派生出另一个库，叫做 [tenacity](https://pypi.org/project/tenacity/) 。今天，我们来讨论一下`tenacity`为我们提供了哪些功能。

# 韧性:

Tenacity 是一个 Apache 2.0 许可的通用重试库，用 Python 编写，用于简化向任何东西添加重试行为的任务。它源于[重试](https://github.com/rholder/retrying/issues/65)的一个分支，遗憾的是不再被[维护](https://julien.danjou.info/python-tenacity/)。Tenacity 与重试不兼容，但增加了重要的新功能，并修复了一些长期存在的错误。

## 安装:

```
$pip install tenacity
```

# 无条件简单重试:

下面的代码，`retries`永远不带任何条件。这是一个基本的重试修饰器，可以应用于任何引发异常的函数。

简单重试

## 输出:

```
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
```

如果没有出现异常，就不会有重试，并且`print`语句将只执行一次，你应该明白了。

# 有条件重试:

## 尝试后停止:

现在我们已经看到了基本的重试，让我们尝试施加条件。在某些情况下，我们可能需要停止尝试某些条件。让我们尝试实现一个这样的条件。

带停止的简单重试

## 输出:

```
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
 Example for simple retry. Retrying forever...
Traceback (most recent call last):
  File "/Users/dkb/VirtualEnvs/practo-env/lib/python3.9/site-packages/tenacity/__init__.py", line 407, in __call__
    result = fn(*args, **kwargs)
  File "/Users/dkb/Code/practice/my_tenacity.py", line 7, in simple_retry
    raise Exception('Raising exception after 5 retries...')
Exception: Raising exception after 5 retries...

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "/Users/dkb/Code/practice/my_tenacity.py", line 10, in <module>
    simple_retry()
  File "/Users/dkb/VirtualEnvs/practo-env/lib/python3.9/site-packages/tenacity/__init__.py", line 324, in wrapped_f
    return self(f, *args, **kw)
  File "/Users/dkb/VirtualEnvs/practo-env/lib/python3.9/site-packages/tenacity/__init__.py", line 404, in __call__
    do = self.iter(retry_state=retry_state)
  File "/Users/dkb/VirtualEnvs/practo-env/lib/python3.9/site-packages/tenacity/__init__.py", line 361, in iter
    raise retry_exc from fut.exception()
tenacity.RetryError: RetryError[<Future at 0x100d64c40 state=finished raised Exception>]
```

## 延迟后停止:

这将尝试，直到我们提供的时间。例如，如果你想尝试 10 秒钟，然后停止，这是你应该选择的方法。

在下面的代码中，[httpbin.org](http://httpbin.org/)是一个测试 HTTP 服务器。您可以选择任何其他 HTTP 服务器进行测试，或者托管您自己的服务器。

## 输出:

```
401
401
401
401
401
401
401
401
401
401
401
401
401
401
401
401
Traceback (most recent call last):
  File "/Users/dkb/VirtualEnvs/practo-env/lib/python3.9/site-packages/tenacity/__init__.py", line 407, in __call__
    result = fn(*args, **kwargs)
  File "/Users/dkb/Code/practice/my_tenacity.py", line 21, in authenticate_user
    response.raise_for_status()
  File "/Users/dkb/VirtualEnvs/practo-env/lib/python3.9/site-packages/requests/models.py", line 953, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 401 Client Error: UNAUTHORIZED for url: http://httpbin.org/basic-auth/user/password
```

在上面的代码中，我们重试 auth 机制十秒钟，然后出错。您总是可以通过封装在一个`try/except`块中来捕捉这些异常，以增加更多的优雅。

## 组合停止条件:

偶尔，服务器需要一段时间来响应，而大多数时候他们可能会很快响应。在某些情况下，您可能还必须量化重试的次数。在这些场景中，你可以结合`stop_after_delay`和`stop_after_attempt`两个条件。

这里，无论哪个条件首先满足，重试都会停止。

## 输出:

```
401
401
401
401
401
RetryError[<Future at 0x110914c40 state=finished raised HTTPError>]
```

# 实施指数回退:

大多数后端服务器限制每秒向服务发出的请求数。在这些情况下，您可能会遇到限制异常或超过最大请求数错误。在这些场景中，我们可能需要抑制我们的请求，以降低对服务器的请求率。

> 如果你有兴趣了解如何解决`aws`中的节流异常，请阅读[这篇](https://dock2learn.com/tech/throttling-exceptions-in-aws-and-ways-to-resolve-them/)帖子。

## 输出:

```
401 2022-06-28 11:54:40.414397
401 2022-06-28 11:54:43.054521
401 2022-06-28 11:54:45.614282
401 2022-06-28 11:54:48.176247
401 2022-06-28 11:54:50.648000
RetryError[<Future at 0x110f7fd30 state=finished raised HTTPError>]
```

这里，我们在尝试重试之前等待 2 秒钟，然后重试 5 次。重试的次数可以改变，如果你想重试，直到你成功。这样，您可以在`retry`装饰器中混合匹配`stop`和`wait`参数。

# 针对特定异常重试:

顾名思义，我们可以重试特定的错误消息，如`HTTP errors`、`ValueError`、`TypeError`或任何自定义异常。

此代码将仅在 HTTPErrors 的情况下重试。如此优雅。不是吗？类似地，如果异常不是特定的类型，我们也可以使用`retry_if_not_exception_type()`。

# 基于返回值的重试和显式重试:

可以将函数编程为基于返回值重试。例如，当一个函数返回`None`时。

## 输出:

```
 Retrying . . .: None
 Retrying . . .: None
 Retrying . . .: None
 Retrying . . .: None
 Retrying . . .: None
 Retrying . . .: None
 Retrying . . .: None
 Retrying . . .: None
 Retrying . . .: None
```

当您使用`TryAgain`异常得到 401 错误时，上面的代码会重试。我没有为此提供输出，因为到现在为止，您已经掌握了它的窍门。

# 统计数据:

使用`statistics`属性可以获得用`retry`修饰的函数的统计数据。

## 输出:

```
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
{'start_time': 0.227001672, 'attempt_number': 5, 'idle_for': 0, 'delay_since_first_attempt': 2.319967352}
```

统计数据以字典的形式打印出来，包括开始时间、尝试次数等。

# 自定义回调:

这个选项是我个人最喜欢的。这正是让`tenacity`成为开发者的乐趣所在。您可以添加一个回调机制，在我们用完重试次数后调用，或者更准确地说，在执行完`retry`装饰器后调用。

这将避免引发异常。如果您想要引发自定义异常而不是`RetryError`，这可能会派上用场。

## 输出:

```
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
 Retrying using explicit retry . . .
 Maximum number of retries exceeded. <RetryCallState 4340277104: attempt #5; slept for 0.0; last result: failed (HTTPError 401 Client Error: UNAUTHORIZED for url: http://httpbin.org/basic-auth/user/password)> 
{'start_time': 0.196902178, 'attempt_number': 5, 'idle_for': 0, 'delay_since_first_attempt': 3.0087615750000003}
```

整洁吗？。这里的`get_response()`是一个回调函数，它接受一个具有当前`retry`所有属性的`retry_state`参数。

# 总结:

在我看来，tenacity 将很快成为重试的同义词库，因为重试不再被维护。话虽如此，我们也将`retry`作为 python 标准库的一部分。然而，我不确定我们是否有像`tenacity`中那样多的参数。除了上面提到的功能，`tenacity`还提供

欲了解更多信息，请参考参考文献部分提供的文档。

下次如果你正在为你的代码实现一个`retry`,我建议你试试 tenacity。

# 参考资料:

*   [https://tenacity.readthedocs.io/en/latest/](https://tenacity.readthedocs.io/en/latest/)
*   [https://pypi.org/project/tenacity/](https://pypi.org/project/tenacity/)

*原载于 2022 年 6 月 29 日 https://dock2learn.com*[](https://dock2learn.com/tech/implement-retries-using-tenacity-in-python/)**。**