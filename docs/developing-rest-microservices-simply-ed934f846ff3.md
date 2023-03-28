# 简单开发 REST 微服务

> 原文：<https://medium.com/codex/developing-rest-microservices-simply-ed934f846ff3?source=collection_archive---------18----------------------->

![](img/6a1cf1eaccc9993f2e9f8361d6f056c1.png)

# **简介**

我是在 kissweb.org[描述的开源 KISS Web 开发框架的作者](https://kissweb.org)它基于后端的 Java 和前端的标准 HTML/JavaScript。由于对不必要的复杂性和为了创建一个 web 应用程序而必须组合在一起的所有部分感到沮丧，我创建了 KISS。KISS 为所有需要的东西提供了一个一站式商店，已经完全集成并开箱即用。

KISS 包括前端、后端、REST 通信、微服务、SQL API、自定义 HTML 标签、身份验证、浏览器缓存控制等等。KISS 代表保持简单……为此，KISS 被设计成易于启动和运行、易于学习和使用。事实上，KISS 可以在大约四个命令行中下载、构建和运行！

KISS 可以在 Linux、Mac 和 Windows 上运行，目前正在一些商业应用程序中使用，因此它是一个经过充分测试的可靠框架。

# 微服务

每个 KISS REST 微服务 web 方法都是在单个 Java 方法中实现的。所以，如果你有四个微服务，你只需要四个 Java 方法。一个“Hello World”微服务可以用一个包含单行代码的方法完全实现！没有布线或配置。因为它们是微服务，所以简单地保存源文件就可以将微服务添加到系统中。KISS 可以识别新的或更改的文件，并在调用它们时即时编译它们。所有 KISS 微服务都是全编译全速运行。

除了 [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) ，KISS 也支持用 [Groovy](http://www.groovy-lang.org/) 或者 Lisp 编写的微服务。Groovy 基本上是一个简化和增强的 Java。标准 Java 可以作为 Groovy 代码运行，Groovy 的简化和增强可以根据需要使用。Groovy 代码也在运行时被编译成常规的 JVM 字节码，所以它也可以全速运行。

Lisp 支持由 [ABCL 项目](https://abcl.org/)提供。它支持完整的 Common Lisp 并动态编译成 JVM 字节码。

在 KISS 中，每个微服务都自动作为 REST web 服务提供。不需要额外编码！KISS REST web 服务是异步的，在一个定义的线程池中运行，因此可以高效地处理大量负载。

我现在将展示定义和使用 KISS REST 微服务所需的全部后端和前端代码。

# 示例-后端零件

这里有一个后端 REST 微服务，它从前端接受两个数字，并返回它们的总和。

```
**package** services;

**import** org.json.JSONObject;
**import** org.kissweb.database.Connection;
**import** org.kissweb.restServer.ProcessServlet;

**public class** MyJavaService {

    **public void** addNumbers(JSONObject injson, JSONObject outjson, Connection db, ProcessServlet servlet) {
        **int** num1 = injson.getInt(**"num1"**);
        **int** num2 = injson.getInt(**"num2"**);
        outjson.put(**"num3"**, num1 + num2);
    }
}
```

就是这样。这就是全部。不需要其他配置。除了方法中的三行代码，其余的都是样板代码。这对于每一个休息微服务是完全相同的。

“MyJavaService”是 web 服务的名称。“addNumbers”是 web 服务中 web 方法的名称。该方法获取从前端发送的 JSON 对象，并将 JSON 对象返回给前端。

这个方法需要一个 JSON 对象，它有两个名为“num1”和“num2”的对象。它获取它们的整数值，将它们相加，然后将总和返回给前端 JSON 对象中名为“num3”的元素。这就是我们所需要的！

输入和输出 JSON 可以是任意复杂或嵌套的。每个 web 服务可以包含任意数量的 web 方法。此外，不需要错误检查。框架会自动处理错误。

# 示例—前端部分

从前端调用后端 web 服务同样容易。此外，虽然可以使用 KISS 前端，但是 KISS 前端不是必需的。这也可以用于其他前端框架(比如 React)。

下面是从普通 JavaScript 调用上述 web 服务所需的所有代码:

```
**const** data = {
    **num1**: *62*,
    **num2**: *38*
};
**let** res = **await** Server.*call*(**'services/MyJavaService'**, **'addNumbers'**, data);
**if** (res.**_Success**) {
    *whatever*(res.num3);
}
```

调用相同服务的另一种方法如下:

```
**const** data = {
    **num1**: *62*,
    **num2**: *38*
};
Server.*call*(**'services/MyJavaService'**, **'addNumbers'**, data).then(res => {
    **if** (res.**_Success**) {
        *whatever*(res.num3);
    }
}
```

对后端的调用会自动处理错误。KISS 还支持同时调用多个服务的能力，在服务到达时处理每个服务，并在它们都被处理后继续。

# 结束语

由于 KISS web 服务是微服务，所以可以在运行的系统上添加、更改或删除这些服务。不需要关闭服务器、重建、重新部署和重新启动服务器。KISS 系统会注意到变化，并根据需要重新编译这些变化。想象一下开发是多么容易！想象一下，能够纠正生产系统中的一个 bug，而不必使系统停机并扰乱整个组织！

KISS 的另一个方面是认证。这些 web 服务调用中的每一个都是由框架完全认证的。您总是可以确定是谁在打电话，以及他们是否被授权。此外，如果你使用 HTTPS，这些电话是完全加密的！

KISS 旨在使构建 web 应用程序尽可能简单。上面显示了该系统的一部分。KISS 包含的东西还多着呢！KISS 还配有三本手册，甚至在 YouTube 上还有一个视频教程系列。看看吧！