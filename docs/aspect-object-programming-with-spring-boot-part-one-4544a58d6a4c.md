# 用 Spring boot 进行方面对象编程(第一部分)

> 原文：<https://medium.com/codex/aspect-object-programming-with-spring-boot-part-one-4544a58d6a4c?source=collection_archive---------4----------------------->

![](img/59452787307593ac7c3b1d5ee514b316.png)

由[马克斯·杜济](https://unsplash.com/es/@max_duz?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

![](img/95b7fcf0f7e8bee974dc1ce90631858e.png)

## **简介:**

世界上的编程每天都在变化，项目在最近几天变得更大。因此，我们需要不断改进编程方法。

今天我来解释 AOP(面向方面编程)。AOP 在任何项目中的日志和异常的常见用法，我们可以在任何框架中使用它，因为它是一种类似于(OOP)面向对象编程的编程方法。

> **在项目中使用 AOP 时，我们可以将业务逻辑从日志和异常中分离出来。这是它的常见用法。**

## spring 中的方面核心概念和方面概念:

AOP 是一种构建通用的、可重用的例程的技术，这些例程可以在应用程序范围内应用。在开发过程中，这有助于核心应用程序逻辑和常见的、可重复的任务(输入验证、日志记录、错误处理等)的分离。

**春天的体概念:**

Spring AOP 从类中取出了横切任务的直接依赖，这是我们通过普通的面向对象编程模型无法实现的。例如，我们可以有一个单独的日志记录类，但是功能类必须调用这些方法来实现应用程序的日志记录。

**方面组件:**

要在 spring-boot 中应用 AOP，可以从 Maven 或 Gradle 中使用 AspectJ 依赖项，或者如果使用 spring，必须在 spring.xml 中配置 aspect

> 在这篇文章中，我使用了 AspectJ 依赖。

*   **方面:**方面是实现跨多个类的企业应用关注点的类，你可以通过`@Aspect`方面类上面的注释来定义它。
*   **切入点:**是一个在事件发生时捕捉事件的表达式，比如方法执行。
*   **建议:**
    方面的建议非常重要，因为每个流程方面都需要建议。Advice 将在切入点发生时采取行动
    **Advice 示例:在方法 X 执行之前，我需要记录字符串 X 或者记录来自方法 X 的一些参数**

> 我们不止有建议类型，我们会在编写代码时看到它，并解释它。

*   **连接点:**连接点是应用程序中的一个特定点，如方法执行、异常处理、改变对象变量值等。在 Spring AOP 中，连接点总是方法的执行。
*   **Object Target** :它是来自原始方法的一个对象，用来知道这个方法现在执行的是什么，或者如果我们需要改变什么，我们可以通过这个对象来完成，实际上，我们可以从 advice 方法的**连接点**中获取对象 Target。
    对象目标将在运行时从 AOP 代理创建。
*   AOP 代理 : Spring AOP 实现使用 JDK 动态代理来创建带有目标类和通知调用的代理类，这些被称为 AOP 代理类。我们也可以使用 **CGLIB** 代理，将它作为依赖项添加到 Spring AOP 项目中。
*   **编织**:将方面与其他对象链接起来，创建被通知的代理对象的过程。这可以在编译时、加载时或运行时完成。Spring AOP 在运行时执行编织。

## **方面建议类型:**

事实上，我们有更多类型的建议，你可以在你的项目中使用它们中的任何一种，每种建议都有自己的工作。我们使用建议在运行时的特定时间对项目中的方法采取行动。

*   **Before:**Before 通知将在进入内部连接点方法之前执行。我们可以用它来记录应用程序运行时的任何语句。
    要在 spring boot 应用程序中应用 **before** 建议，我们需要使用上面的`@Before`注释 before 方法。
*   **After:**After 建议将在连接点方法完成后执行。
    要在 spring boot 应用程序中的建议后应用**，我们需要在方法后使用上面的`@After`注释。**
*   **返回后:**有时我们需要记录除返回值后而不是最终完成后的任何东西，在这种情况下，我们需要使用**返回后建议**。
    要在 spring boot 应用程序中返回建议后应用**，我们需要在返回**方法后使用`@AfterReturning`上面的注释**。**
*   **抛出后:**项目内部总是需要一个日志异常。在抛出注释后使用**时，AspectJ 支持这个特性。
    要在 spring boot 应用程序中应用**后抛出**建议我们需要使用`@AfterThrowing`上面注释的后抛出方法。**
*   **Around:** 通过 Around Advice 我们可以从 **Around advice 方法**中执行**连接点**，或者我们可以不从 aspect 类中执行它，正常运行。
    从 Around 方法中，我们可以在执行 **Joinpoint** 之前或之后执行它内部的一些建议。但是在建议前后使用**的时候一定要小心。因为绕行建议可以为您的**连接点**改变运行方式。
    要在 spring boot 应用程序中应用 Around 建议，我们需要使用上述 Around 方法的注释。**

**结论:**

现在我们知道了 AOP(面向方面编程)的概念，并且知道何时需要使用它。实际上，这还不够，因为我们直到现在才应用关于方面的编码。但是第二篇文章将是关于 spring boot 的方面编码。

出版后，你可以在这里找到链接。

> **别忘了在文章上拍手，每篇文章可以拍手 50 次。还有你在这里找到我的**[**LinkedIn**](https://www.linkedin.com/in/abd-alrhman-alkraien-83a93a1b1/)

**参考文献:**

[](https://docs.spring.io/spring-framework/docs/4.3.12.RELEASE/spring-framework-reference/html/aop.html) [## 11.用 Spring 进行面向方面编程

### 面向方面编程(AOP)通过提供另一种思维方式来补充面向对象编程(OOP)

docs.spring.io](https://docs.spring.io/spring-framework/docs/4.3.12.RELEASE/spring-framework-reference/html/aop.html) [](https://www.journaldev.com/2583/spring-aop-example-tutorial-aspect-advice-pointcut-joinpoint-annotations) [## Spring AOP 示例教程——方面、建议、切入点、连接点、注释、XML 配置…

### Spring 框架是基于两个核心概念开发的——依赖注入和面向方面编程(Spring…

www.journaldev.com](https://www.journaldev.com/2583/spring-aop-example-tutorial-aspect-advice-pointcut-joinpoint-annotations)