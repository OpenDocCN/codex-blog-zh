# Spring Web 和 WebFlux 异常处理最佳实践

> 原文：<https://medium.com/codex/spring-web-and-webflux-exception-handling-best-practices-b2c3cd7e3acf?source=collection_archive---------3----------------------->

# 问题

在 web 应用程序上工作，编写异常处理类只是时间问题，有很多方法可以做到这一点。特别是，我们的目标是控制我们的 API 客户端如何看到错误，例如从控制器抛出的异常。

*如果你真的是一个网游高手——跳到 GitHub* *上的* [*示例源代码。它的结构很好，包括测试来证明这个概念。*](https://github.com/artemptushkin/spring-web-exception-handling)

# 代码设计期望

让我们在这里谈谈我们真正想要达到的目标。

首先，我们应该定义日常工作和它添加另一个异常或跟踪当前异常的行为。

第二，我们应该与客户就合同达成一致，并且，我们可能希望返回相同的数据结构，以保持所有内容的简洁。此外，我们希望将该解决方案扩展到我们公司/项目中的其他服务。

*   该解决方案应该是可扩展的，我们有一个简单的方法来添加一个管理异常
*   应该很容易追溯出异常来自哪里
*   我们应该在一个文件中找到已知异常的列表及其要映射的状态代码
*   它应该无任何排除地映射所有发生的异常，并且后者应该在这个单独的代码位置中被控制

# 网

一般来说，有两种方法可以处理来自 web 的异常:

*   `@ControllerAdvice` + `@ExceptionHandler`
*   用`@ResponseStatus`标注异常类

第二种方法没有提供映射消息自定义的选项，最终您会采用第一种方法，这种方法更加灵活。

spring 中设计异常处理时最普遍的错误是，当过滤器抛出一个异常时，你不会捕捉到它`ExceptionHandler`，因为它永远不会到达`DispatcherServlet`。如果你幸运的话，你可以在网上看到这个，例如，[这里的](https://stackoverflow.com/a/34598356/7785738)是一个 StackOverflow 评论，已经被标记了 45 次，讲述了这个。

我们应该关注过滤器的原因是:

*   改天您可能会有一个带有过滤器的启动器用于请求认证
*   你公司栈中的任何库都可能包含一个你不知道的过滤器，它会在另一天抛出一个你在`ExceptionHandler`中捕捉不到的异常

因此，这里的长期解决方案是扩展`OncePerRequestFilter`，因为它写在完全相同的 [StackOverflow 答案](https://stackoverflow.com/questions/34595605/how-to-manage-exceptions-thrown-in-filters-in-spring)中，但是如果你或你的同事没有提出适当的问题，他们可能不会到达那里。当你面对一个问题并想恰当地解决它时，这个问题通常会出现。

# 结合

后一种解决方案是以一种松散耦合的方式将`OncePerRequestFilter`和`ExceptionHandler`结合起来，以便将来能够轻松地打破这种依赖。它应该是一些*异常处理过滤器，*像:

这个类委托给你用`ControllerAdvice`注释的异常处理程序，因此我们所有的异常都在你的代码库中的一个**位置！**

剩下的就是考虑一种简单的方法来将您的异常转换成您需要的域响应，例如:

```
{
    "message": "I'm a demo exception from controller",
    "code": 400
}
```

最具伸缩性的数据结构不需要你写样板文件*如果*代码每次都是**映射**并且在 Spring 中你总是可以定义它为一个 bean:

让您像这样映射您处理新异常的日常工作很简单——只需在映射中添加另一个条目！

# Bean 配置

您可以在这里找到一个示例 bean 配置[。请关注 sprig security 的这次更新，并查看](https://github.com/artemptushkin/spring-web-exception-handling/tree/master/src/main/java/io/github/artemptushkin/demo/springwebexceptionhandling/config) [Spring 博客了解更多](https://spring.io/blog/2022/02/21/spring-security-without-the-websecurityconfigureradapter)。

> 在 Spring Security 5.7.0-M2 中，我们[弃用了](https://github.com/spring-projects/spring-security/issues/10822)的`WebSecurityConfigurerAdapter`，因为我们鼓励用户转向基于组件的安全配置

# WebFlux

在 WebFlux 中，[有很多方法来处理来自 web 层的异常](https://www.baeldung.com/spring-webflux-errors)。但好消息是——不需要像我们在 web 中那样考虑过滤器问题，您可以定义一个通用的全局异常处理程序，并在您的服务中从它开始。所有的异常都将被处理，请在我的知识库中找到验证测试。

以同样方式依赖于地图的 WebFlux 异常处理程序如下所示:

# 默认状态代码

保持松散耦合的重要时刻是将默认状态代码外部化，以便在出现意外异常时做出响应。我们在上面的 web 示例中也有这种方法。

有了这样的代码，很容易:

*   用任何其他默认响应代码测试您的异常处理程序
*   如果您将类移动到一个库中，您就可以让您的用户更改默认行为

# 摘要

使用 Spring 实现松散耦合的代码是可能的，这将有利于:

*   测试
*   重构
*   分解

总的来说，试着专注于:

*   定义接口
*   通过构造函数传递所有内容(不要在构造函数内部初始化对象属性)
*   将所有东西初始化为 bean
*   测试

你可以在 [my repository](https://github.com/artemptushkin/spring-web-exception-handling) 中找到如何测试你的异常处理功能的例子。测试场景包括文章中描述的所有重要的具体案例。它包括 web 和 webflux 设置，以及在这两者之间切换测试的简洁方法。