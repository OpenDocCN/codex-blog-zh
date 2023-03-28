# 用 httprouter 创建中间件

> 原文：<https://medium.com/codex/creating-middleware-with-httprouter-52cbc786b02a?source=collection_archive---------4----------------------->

![](img/d92dd167ffcb2eef9fe6ed96884851a5.png)

[贾梅特琳·雷斯克](https://unsplash.com/@reskp?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/directions?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

在 Go 中使用基于 HTTP 的服务时，许多开发人员(包括我自己)喜欢使用 HTTP 多路复用器(mux)。多路复用器，有时也称为请求路由器，提供一系列特性和功能。最简单的形式允许用户用特定的 URL 模式和方法注册特定的处理函数。

更高级的特性之一是使用 HTTP 多路复用器来创建“HTTP 中间件”此功能允许用户在请求路由到用户定义的处理程序之前创建一个标准函数或一组已执行的函数。开发人员可以使用这些通用函数来执行许多任务，从创建标准日志程序到处理身份验证。

今天的文章将探索使用 httprouter HTTP 多路复用器来创建一个简单的 HTTP 中间件。

# 为什么是 httprouter

有许多可用的多路复用器，常见的有 [Gorilla Mux](https://github.com/gorilla/mux) 、 [httprouter](https://github.com/julienschmidt/httprouter) 和 [Bone](https://github.com/go-zoo/bone) 。对于本文，我将探索 httprouter，我经常使用它，因为我喜欢它的简单和高效。还因为有一个版本同时适用于`net/http`和`fasthttp`。我在我的各种项目中使用了这两个 HTTP 包。不管我使用哪一个，只要有一个 HTTP 多路复用器就可以使用，这有助于保持简单。

# httprouter 入门

在开始创建 HTTP 中间件功能之前，我们应该首先从使用 httprouter 的基础开始。我们可以看看打印“Hello World”的最基本的 HTTP 服务来启动我们。

上面的代码只使用了标准的`net/http`和带有标准库的默认 mux。Go 的一个好处是，如果您要构建基本的基于 HTTP 的服务，您只需要标准库。

但是对于这篇文章，我们想做一些更高级的东西；我们希望在 HTTP 处理程序之前执行一个标准的 HTTP 中间件。

首先，让我们将 httprouter 添加到这个组合中。

从上面的代码示例中可以看出，我们对代码做了一些修改。首先要调用的是，我们没有使用`http.HandleFunc()`来注册我们的`handler`函数，而是创建了一个`httprouter.Router`,并用它来为`/hello`端点注册我们的`handler`函数。

敏锐的眼睛可能还会注意到，使用 httprouter，我们可以在不同的 HTTP 方法下注册我们的函数。这个特性是 httprouter 优于默认标准库的优秀特性之一。使用 httprouter，用户可以为不同的 HTTP 方法注册不同的函数。在我们的示例中，在 GET 之外的任何方法下服务于`/hello`路由都没有意义。

除了注册`handler`函数的方式发生了变化，值得一提的是`handler`函数的签名也发生了变化。现在，除了标准的`http.ResponseWriter`和`http.Request`类型，我们的函数还接收了一个`httprouter.Params`类型。

使用 httprouter，更高级的特性之一是参数化 URI 的能力。例如，我们可以注册`/hello/:name`，而不是注册`/hello`。我们可以要求用户在 URI 中输入他们的名字，比如`/hello/ben`。然后，我们可以使用提供给处理程序的`httprouter.Params`来访问该参数。

我们现在有了一个使用 httprouter 的完全正常工作的 HTTP 服务；我们可以从这里开始创建我们的中间件功能。

# 创建我们的 HTTP 中间件

对于本文，我们将创建一个基本的日志中间件。这个中间件旨在创建 HTTP 请求的一致日志，而不管 HTTP 处理程序执行什么。这意味着在我们的`handler()`函数执行之前，我们希望我们的中间件先运行。

我们将通过创建一个包装器函数，然后在我们的`handler()`注册调用中包含这个包装器函数来实现这一点。

在上图中，我们可以看到我们的包装函数`middleware()`现在正和`handler()`函数一起注册。这种技术的名称是链接；通过用`middleware()`函数包装`handler()`函数，我们将两个函数链接在一起。首先执行`middleware()`函数，最后，`middleware()`函数本身调用我们的`handler()`函数。

至此，我们有了一个由 httprouter 支持的工作的 HTTP 中间件。对于我们拥有的每个自定义处理程序，我们可以使用这个 HTTP 中间件来保持一致的日志。但是非定制处理程序呢？像 [PProf](https://pkg.go.dev/net/http/pprof) 这样用标准 HTTP 签名构建处理程序的东西呢？

# 向 HTTP 中间件注册非定制处理程序

PProf 等库的一个常见做法是提供 HTTP 处理程序作为包接口的一部分。`net/http/pprof`包有几个遵循标准`net/http`方法签名的函数。诸如 Index 和 Profile 之类的函数，Index 处理`/debug/pprof/`端点，Profile 服务于`/debug/pprof/profile` CPU 分析端点。

问题是这些函数只接受两个输入，`http.ResponseWriter`和`http.Request`。它们与我们的 HTTP 中间件的签名不匹配，但是我们仍然希望将我们的中间件用于这些功能。

幸运的是，我们可以。使用`http.HanderFunc()`适配器，我们可以将这些功能转换成`http.Handler`的功能。然后，我们可以使用另一个小包装器将这个`http.Handler`与我们的中间件包装在一起。

从上面的代码中我们可以看到，我们添加了一个输入为`http.Handler`的`wrapper()`函数，这个包装函数正在用我们的中间件包装一个运行`http.Handler.ServeHTTP()`的匿名函数。

通过添加这个`wrapper()`函数，我们现在可以在任何标准的 HTTP 处理程序上引入我们的 HTTP 中间件，不管它是来自 PProf 还是其他库。

现在我们已经定义了`wrapper()`函数，让我们继续添加 PProf 的其余部分。

在上面的例子中，敏锐的眼睛可能注意到在注册`/debug/pprof`路径的中途，我们停止使用`http.HandlerFunc()`适配器。相反，我们注册了一个带有各种参数的`pprof.Handler()`函数。通过返回一个`http.Handler`类型的`pprof.Handler()`函数，PProf 包满足了一些路径。

# 摘要

有了以上内容，我们就有了一个简单的 hello world 应用程序，它展示了用 httprouter 创建 HTTP 中间件功能是多么容易。我们还可以看到如何使用这个中间件，甚至是 PProf 这样的包，其中 HTTP 处理程序使用不同的签名。

如果您构建一个包含许多路由和不同处理程序的复杂服务，那么诸如 httprouter 这样的路由器可能是一个很好的工具。但是 Go 最好的一点是，如果你需要做的只是简单的 HTTP 服务，除了标准库之外，你不需要太多。

*原载于*[](https://bencane.com/2021/09/08/creating-middleware-with-httprouter-a-golang-multiplexer/)**。**