# Spring Boot-创建自定义注释以验证请求参数

> 原文：<https://medium.com/codex/spring-boot-create-custom-annotation-to-validate-request-parameter-dcf483539d90?source=collection_archive---------0----------------------->

![](img/48b53f9a411a1bf11de144d7e4d9fe35.png)

Clint Patterson 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

当我在 Spring Boot 项目中工作时，我发现定制注释可以用于验证。理解它如何工作以及我们如何使用定制注释进行验证的最好方法是创建简单的用例，并尝试理解它如何工作。在这里，我将创建一个自定义注释来验证任何请求参数中允许的值。

**什么是标注？**

注释只是关于字段、参数、方法、构造函数、类型(*类/接口/枚举*)、包和注释类型的元数据。

Java 注释是一种向源代码添加元数据信息的机制。它们是 JDK5 中添加的 Java 的强大部分。注释提供了使用 XML 描述符和标记接口的替代方法。

虽然我们可以将它们附加到包、类、接口、方法和字段中，但是注释本身对程序的执行没有任何影响。

1.  **添加 spring-boot-starter-validation 依赖项。**

web 启动器中不再包含 Validation starter。spring-boot-starter-validation 不再是 spring-boot-starter-web 和 spring-boot-starter-webflux 的过渡依赖项。

为验证工作添加此依赖项。

2.**创建自定义注释**

这里，我们将添加一个约束来验证任何请求参数字段。我们的验证器将有三个字段:第一个是`propName`，它将定义任何自定义属性；第二个是`values`，它将包含 propName 允许值的列表；第三个是`message`，它将保存错误消息。

```
@Constraint(validatedBy = ValuesAllowed.Validator.class)
```

当验证失败时，我返回针对@ValuesAllowed 注释提到的错误消息。

```
String message() default "Field value should be from list of ";
```

我通过实现接口 ConstraintValidator 来添加验证逻辑。我在被覆盖的 initialize 方法中初始化接口变量。

在被覆盖的`isValid`方法中，我正在执行一个简单的检查，看传递的值是否出现在允许值的列表中。如果不存在，则抛出一条错误消息。

3.**在控制器中添加验证。**

在这里，我添加了对`orderBy`请求参数的注释。我将在注释中为`orderBy`请求传入一个自定义属性名和允许值列表。

用 `@Validated`注释对控制器进行注释，否则根本不会触发注释`ValuesAllowed`。

**注**

如果发生任何验证错误，上面的代码将返回 HTTP 500，并使用难看的堆栈跟踪污染日志。为了避免这种情况，我们可以在控制器主体中放入一个`@ExceptionHandler`方法(这样它的作用范围就仅限于这个控制器),并且您可以获得对 HTTP 状态的控制:

```
@ExceptionHandler(ConstraintViolationException.class)
@ResponseStatus(HttpStatus.BAD_REQUEST)
String handleConstraintViolationException(ConstraintViolationException e) {
    return "Validation error: " + e.getMessage();
}
```

…或者您可以将该方法放入单独的`@ControllerAdvice`类中，并对该验证进行更多的控制，例如在所有控制器上使用它，或者只在需要的控制器上使用它。

如果你喜欢这篇文章，请花点时间给我鼓掌👏(可以多次鼓掌)，关注我，甚至请我喝咖啡[https://www.buymeacoffee.com/abhiandy](https://www.buymeacoffee.com/abhiandy)