# 实用函数式 Java 简介

> 原文：<https://medium.com/codex/introduction-to-pragmatic-functional-java-md-fba6bdaae6a8?source=collection_archive---------0----------------------->

实用函数式 Java (PFJ)试图定义一种新的惯用 Java 编码风格。编码风格，它将完全利用当前和未来 Java 版本的所有特性。编码风格，这将涉及编译器，以帮助编写简洁而可靠和可读的代码。

虽然这种风格甚至可以在 Java 8 中使用，但在 Java 11 中，它看起来更加简洁明了。在 Java 17 中，它变得更有表现力，并受益于每一个新的 Java 语言特性。

但是 PFJ 不是免费的午餐，它需要开发者在习惯和方法上的重大改变。改变习惯并不容易，传统的命令式习惯尤其难以处理。

值得吗？肯定！PFJ 代码简洁、表达能力强、可靠、易于阅读和维护。在大多数情况下，如果代码能够编译——它就能工作！

(本文是[语用学](https://github.com/siy/pragmatica)库的组成部分)。

## 实用函数式 Java 的要素

PFJ 源自精彩的 [Effective Java](https://www.amazon.com/dp/0134685997/) 书，带有一些额外的概念和约定，特别是源自函数式编程。

请注意，尽管使用了 FP 概念，PFJ 并没有试图强制使用特定于 FP 的术语。(尽管为那些有兴趣进一步探索这些概念的人提供了参考)。

PFJ 专注于:

*   减少精神开销
*   提高代码可靠性
*   提高长期可维护性
*   让编译器帮助编写正确的代码
*   使编写正确的代码变得容易和自然；编写不正确的代码，虽然仍然有可能，但应该需要努力

尽管目标远大，但只有两条关键的 PFJ 规则:

*   尽可能避免`null`
*   没有业务例外

下面，我们将更详细地探讨每个关键规则:

## 尽可能避免`null`(地图规则)

变量的可空性是[特殊状态](https://dev.to/siy/leveraging-java-type-system-to-represent-special-states-688)之一。它们是运行时错误和样板代码的众所周知的来源。为了消除这些问题并表示可能丢失的值，PFJ 使用了[选项](https://github.com/siy/pragmatica/blob/main/core/src/main/java/org/pfj/lang/Option.java)容器。这涵盖了可能出现这种值的所有情况—返回值、输入参数或字段。

在某些情况下，例如出于性能或与现有框架兼容的原因，类可能会在内部使用`null`。这些情况必须清楚地记录下来，并且对类用户不可见，即所有的类 API 都应该使用`Option<T>`。

这种方法有几个优点:

*   可空变量在代码中立即可见。无需阅读文档/检查源代码/依赖注释。
*   编译器区分可空变量和不可空变量，并防止它们之间的不正确赋值。
*   取消了`null`检查所需的所有样板文件。

## 没有业务例外(NBE 规则)

PFJ 只使用异常来代表致命的、不可恢复的(技术)故障。这种异常可能仅出于记录和/或应用程序正常关闭的目的而被拦截。不鼓励并尽可能避免所有其他异常及其拦截。

业务异常是[特殊状态](https://dev.to/siy/leveraging-java-type-system-to-represent-special-states-688)的另一种情况。对于业务级错误的传播和处理，PFJ 使用[结果](https://github.com/siy/pragmatica/blob/main/core/src/main/java/org/pfj/lang/Result.java)容器。

同样，这涵盖了可能出现错误的所有情况—返回值、输入参数或字段。实践表明，字段很少(如果有的话)需要使用这个容器。

当可以使用业务级异常时，没有合理的情况。通过专用的包装方法实现与现有 Java 库和遗留代码的接口。[结果](https://github.com/siy/pragmatica/blob/main/core/src/main/java/org/pfj/lang/Result.java)容器包含这些包装方法的实现。

`No Business Exceptions`规则提供了以下优势:

*   可以返回错误的方法在代码中立即可见。无需阅读文档/检查源代码/分析调用树来检查哪些异常可以抛出以及在哪些条件下抛出。
*   编译器强制执行正确的错误处理和传播。
*   几乎没有错误处理和传播的样板文件。
*   可以为*快乐日场景*编写代码，并在最方便的时候处理错误——这是异常的最初意图，但实际上从未实现。
*   代码仍然是可组合的，易于阅读和推理，在执行流程中没有隐藏的中断或意外的转换— *您所阅读的就是将要执行的*。

## 将遗留代码转换为 PFJ 风格的代码

好吧，关键规则看起来很好很有用，但是真正的代码会是什么样子呢？

让我们从非常典型的后端代码开始:

> `public interface UserRepository {
> User findById(User.Id userId);
> }
> 
> public interface UserProfileRepository {
> UserProfile findById(User.Id userId);
> }
> 
> public class UserService {
> private final UserRepository userRepository;
> private final UserProfileRepository userProfileRepository;
> 
> public UserWithProfile getUserWithProfile(User.Id userId) {
> User user = userRepository.findById(userId);
> 
> if (user == null) {
> throw UserNotFoundException("User with ID " + userId + " not found");
> }
> 
> UserProfile details = userProfileRepository.findById(userId);
> 
> return UserWithProfile.of(user, details == null
> ? UserProfile.defaultDetails()
> : details);
> }
> }`

示例开头的接口是为了上下文清晰而提供的。

主要的兴趣点是`getUserWithProfile`方法。我们一步步来分析吧。

*   第一条语句从用户存储库中检索`user`变量。
*   由于用户可能不在存储库中，`user`变量可能是`null`。接下来的`null`检查验证是否是这种情况，如果是，则抛出业务异常。
*   下一步是检索用户配置文件的详细信息。缺少细节不被认为是错误。相反，当缺少详细信息时，将为配置文件使用默认值。

上面的代码有几个问题。首先，从界面上看，如果值不在存储库中，则返回`null`。我们需要检查文档，研究实现或者猜测这些存储库是如何工作的。有时注释被用来提供提示，但是这仍然不能保证 API 的行为。

为了解决这个问题，让我们对存储库应用 [*ANAMAP*](#avoid-null-as-much-as-possible-anamap-rule) 规则:

```
public interface UserRepository {
    Option<User> findById(User.Id userId);
}

public interface UserProfileRepository {
    Option<UserProfile> findById(User.Id userId);
}
```

现在不需要做任何猜测——API 明确地告诉我们返回值可能不存在。

现在我们再来看看`getUserWithProfile`方法。要注意的第二件事是，该方法可能返回值，也可能抛出异常。这是一个业务异常，所以我们可以应用 [*NBE*](#no-business-exceptions-nbe-rule) 规则。变更的主要目标——明确方法可能返回值*或*错误的事实:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
```

好了，现在我们已经清理了 API，可以开始修改代码了。第一个变化是由以下事实引起的，即`userRepository`现在返回`Option<User>`:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        Option<User> user = userRepository.findById(userId);
    }
```

现在我们需要检查用户是否存在，如果不存在，返回一个错误。对于传统的命令式方法，代码应该是这样的:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        Option<User> user = userRepository.findById(userId);

        if (user.isEmpty()) {
            return Result.failure(Causes.cause("User with ID " + userId + " not found"));
        }
    }
```

代码看起来不太吸引人，但也不比原始代码差，所以现在让我们保持原样。

下一步是尝试转换代码的剩余部分:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        Option<User> user = userRepository.findById(userId);

        if (user.isEmpty()) {
            return Result.failure(Causes.cause("User with ID " + userId + " not found"));
        }

        Option<UserProfile> details = userProfileRepository.findById(userId);

    }
```

问题来了:细节和用户存储在`Option<T>`容器中，所以为了组装`UserWithProfile`,我们需要以某种方式提取值。这里可能有不同的方法，例如，使用`Option.fold()`法。生成的代码肯定不好看，而且很可能会违反 [*反图*](#avoid-null-as-much-as-possible-anamap-rule) 规则。

还有另一种方法——利用`Option<T>`是一个具有特殊属性的[容器这一事实。特别是，可以使用`Option.map()`和`Option.flatMap()`方法转换`Option<T>`内部的值。此外，我们知道`details`值要么由存储库提供，要么替换为默认值。为此，我们可以使用`Option.or()`方法从容器中提取细节。让我们试试这些方法:](#brief-technical-overview-of-optiont-and-resultt)

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        Option<User> user = userRepository.findById(userId);

        if (user.isEmpty()) {
            return Result.failure(Causes.cause("User with ID " + userId + " not found"));
        }

        UserProfile details = userProfileRepository.findById(userId).or(UserProfile.defaultDetails());

        Option<UserWithProfile> userWithProfile =  user.map(userValue -> UserWithProfile.of(userValue, details));

    }
```

现在我们需要编写最后一步——将`userWithProfile`容器从`Option<T>`转换为`Result<T>`:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        Option<User> user = userRepository.findById(userId);

        if (user.isEmpty()) {
            return Result.failure(Causes.cause("User with ID " + userId + " not found"));
        }

        UserProfile details = userProfileRepository.findById(userId).or(UserProfile.defaultDetails());

        Option<UserWithProfile> userWithProfile =  user.map(userValue -> UserWithProfile.of(userValue, details));

        return userWithProfile.toResult(Cause.cause(""));
    }
```

让我们暂时将`return`语句中的错误原因保留为空，并再次查看代码。我们很容易发现一个问题:我们肯定知道`userWithProfile`总是存在——当`user`不存在时，上面已经处理过了。我们如何解决这个问题？

注意，我们可以调用`user.map()`而不检查用户是否存在。仅当`user`存在时，转换才会被应用，否则会被忽略。这样，我们可以消除`if(user.isEmpty())`检查。让我们将`details`的检索和`User`到`UserWithProfile`的转换移到传递给`user.map()`的 lambda 中:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        Option<UserWithProfile> userWithProfile = userRepository.findById(userId).map(userValue -> {
            UserProfile details = userProfileRepository.findById(userId).or(UserProfile.defaultDetails());
            return UserWithProfile.of(userValue, details);
        });

        return userWithProfile.toResult(Cause.cause(""));
    }
```

现在需要更改最后一行，因为`userWithProfile`可能会丢失。错误将与前一版本相同，因为只有当`userRepository.findById(userId)`返回的值丢失时`userWithProfile`才可能丢失:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        Option<UserWithProfile> userWithProfile = userRepository.findById(userId).map(userValue -> {
            UserProfile details = userProfileRepository.findById(userId).or(UserProfile.defaultDetails());
            return UserWithProfile.of(userValue, details);
        });

        return userWithProfile.toResult(Causes.cause("User with ID " + userId + " not found"));
    }
```

最后，我们可以内联`details`和`userWithProfile`，因为它们只在创建后立即使用一次:

```
public Result<UserWithProfile> getUserWithProfile(User.Id userId) {
        return userRepository.findById(userId)
            .map(userValue -> UserWithProfile.of(userValue, userProfileRepository.findById(userId)
                                                                                 .or(UserProfile.defaultDetails())))
            .toResult(Causes.cause("User with ID " + userId + " not found"));
    }
```

请注意缩进是如何帮助将代码分组为逻辑链接的部分的。

让我们分析结果代码。

*   代码更加简洁，是为`happy day scenario`编写的，没有明显的错误或`null`检查，没有对业务逻辑的干扰
*   没有简单的方法可以跳过或避免错误或`null`检查，编写正确可靠的代码是简单而自然的。

不太明显的观察结果:

*   所有类型都是自动派生的。这简化了重构并消除了不必要的混乱。如果需要，仍然可以添加类型。
*   如果在某个时候存储库将开始返回`Result<T>`而不是`Option<T>`，代码将保持不变，除了最后的转换(`toResult`)将被移除。
*   除了用`Option.or()`方法替换三元运算符之外，产生的代码看起来很像我们将代码从 lambda 内部的原始`return`语句转移到`map()`方法。

最后一个观察对于开始方便地编写(阅读通常不是问题)PFJ 风格的代码非常有用。可以改写成以下经验法则:*在右边找值*。只需比较:

```
User user = userRepository.findById(userId);    // <-- value is on the left side of the expression
```

和

```
return userRepository.findById(userId)
                      .map(user -> ...); // <-- value is on the right side of the expression
```

这个有用的观察有助于从遗留命令式代码风格到 PFJ 的转换。

## 与遗留代码接口

不用说，现有的法规没有遵循 PFJ 的方法。它抛出异常，返回`null`等等。有时可以修改这些代码，使其与 PFJ 兼容，但通常情况并非如此。对于外部库和框架来说尤其如此。

## 调用遗留代码

遗留代码调用有两个主要问题。它们中的每一个都与违反相应的 PFJ 规则有关:

## 处理业务异常

`Result<T>`包含一个名为`lift()`的助手方法，它涵盖了大多数用例。方法签名看起来是这样的:

```
static <R> Result<R> lift(FN1<? extends Cause, ? super Throwable> exceptionMapper, ThrowingSupplier<R> supplier)
```

第一个参数是将一个异常转换成`Cause`实例的函数(它反过来用于在失败的情况下创建`Result<T>`实例)。

第二个参数是 lambda，它包装了对需要与 PFJ 兼容的实际代码的调用。

在`Causes`实用程序类:`fromThrowable()`中提供了最简单的函数，它将异常转换为`Cause`的实例。与`Result.lift()`一起，它们可以如下使用:

```
public static Result<URI> createURI(String uri) {
    return Result.lift(Causes::fromThrowable, () -> URI.create(uri));
}
```

## 处理`null`值返回

这种情况相当简单——如果 API 可以返回`null`,只需使用`Option.option()`方法将其包装到`Option<T>`中。

## 提供传统 API

有时有必要允许遗留代码调用以 PFJ 风格编写的代码。特别是，当一些较小的子系统被转换为 PFJ 风格，但是系统的其余部分仍然以旧风格编写并且 API 需要保留时，这种情况经常发生。

最方便的方法是将实现分成两部分——PFJ 风格的 API 和适配器，它只使新 API 适应旧 API。下面是非常有用的简单帮助器方法，如下所示:

```
public static <T> T unwrap(Result<T> value) {
    return value.fold(
        cause -> { throw new IllegalStateException(cause.message()); },
        content -> content
    );
}
```

由于以下原因，`Result<T>`中没有提供随时可用的帮助器方法:

*   可能有不同的用例，并且可能抛出不同类型的异常(检查的和未检查的)。
*   将`Cause`转换成不同的特定异常严重依赖于特定的用例。

## 管理变量范围

这一部分将致力于编写 PFJ 风格代码时出现的各种实际案例。

以下示例假设使用`Result<T>`，但这在很大程度上是不相关的，因为所有考虑因素也适用于`Option<T>`。此外，示例假设示例中调用的函数被转换为返回`Result<T>`而不是抛出异常。

## 嵌套范围

函数式代码大量使用 lambdas 来执行`Option<T>`和`Result<T>`容器中的值的计算和转换。每个 lambda 都隐式地为它们的参数创建了作用域——它们在 lambda 主体内部是可访问的，但在主体外部是不可访问的。一般来说，这是一个有用的属性，但是对于传统的命令式代码来说，这是很不寻常的，可能一开始会觉得不方便。幸运的是，有一个简单的技术可以克服感觉到的不便。

让我们来看看下面的命令式代码:

```
var value1 = function1(...);                    // function1() may throw exception
var value2 = function2(value1, ...);            // function2() may throw exception
var value3 = function3(value1, value2, ...);    // function3() may throw exception
```

变量`value1`应该可以被`function2()`和`function3()`调用。这确实意味着简单地转换成 PFJ 风格是行不通的:

```
function1(...)
       .flatMap(value1 -> function2(value1, ...))
       .flatMap(value2 -> function3(value1, value2, ...)); // <-- ERROR, value1 is not accessible!
```

为了保持值的可访问性，我们需要使用*嵌套作用域*，即如下嵌套调用:

```
function1(...)
       .flatMap(value1 -> function2(value1, ...)
           .flatMap(value2 -> function3(value1, value2, ...)));
```

对`flatMap()`的第二次调用是针对`function2`返回的值，而不是针对第一次`flatMap()`返回的值。通过这种方式，我们将`value1`保留在范围内，并使其可被`function3`访问。

尽管可以任意深度嵌套作用域，但通常超过两个嵌套作用域会更难阅读和理解。在这种情况下，强烈建议将更深的作用域提取到专用函数中。

## 平行范围

另一个常见的情况是需要计算/检索几个独立的值，然后进行调用或构建一个对象。让我们看看下面的例子:

```
var value1 = function1(...);    // function1() may throw exception
var value2 = function2(...);    // function2() may throw exception
var value3 = function3(...);    // function3() may throw exception

return new MyObject(value1, value2, value3);
```

乍一看，向 PFJ 风格的转换可以完全按照[嵌套作用域](#nested-scopes)来完成。每个值的可见性将与命令式代码相同。不幸的是，这将使作用域嵌套得很深，尤其是在需要获取很多值的情况下。

对于这种情况，`Option<T>`和`Result<T>`提供了一套`all()`方法。这些方法执行所有值的“并行”计算，并返回专用版本的`MapperX<...>`接口。这个接口只有三个方法- `id()`、`map()`和`flatMap()`。`map()`和`flatMap()`方法的工作方式与`Option<T>`和`Result<T>`中的相应方法完全相同，只是它们接受的 lambdas 参数数量不同。让我们看看它在实践中是如何工作的，并将上面的命令式代码转换成 PFJ 风格:

```
return Result.all(
          function1(...), 
          function2(...), 
          function3(...)
        ).map(MyObject::new);
```

除了紧凑和扁平之外，这种方法没有更多的优点。首先，它明确表达了意图——在使用之前计算所有值。命令式代码按顺序执行，隐藏了原始意图。第二个优势—每个值的计算都是独立的，不会将不必要的值带入范围。这减少了理解和推理每个函数调用所需的上下文。

## 替代范围

一种不太常见但仍然重要的情况是，当我们需要检索值时，但是如果它不可用，那么我们使用值的替代来源。有多种选择的情况就更少了，但是当涉及到错误处理时就更痛苦了。

让我们看看下面的命令式代码:

```
MyType value;

try {
    value = function1(...);
} catch (MyException e1) {
    try {
        value = function2(...);    
    } catch(MyException e2) {
        try {
            value = function3(...);
        } catch(MyException e3) {
            ... // repeat as many times as there are alternatives 
        }
    }
}
```

代码有些做作，因为嵌套的案例通常隐藏在其他方法中。然而，整体逻辑远非简单，主要是因为除了选择值之外，我们还需要处理错误。错误处理使代码变得混乱，并使最初的意图—选择第一个可用的替代方案—隐藏在错误处理中。

向 PFJ 风格的转变让意图昭然若揭:

```
var value = Result.any(
        function1(...),
        function2(...),
        function3(...)
    );
```

不幸的是，这里有一个重要的区别:最初的命令式代码只在必要时才计算第二个和随后的选项。在某些情况下，这不是一个问题，但在许多情况下，这是非常不可取的。还好有`Result.any()`的懒人版。使用它，我们可以重写代码如下:

```
var value = Result.any(
        function1(...),
        () -> function2(...),
        () -> function3(...)
    );
```

现在，转换后的代码的行为与命令式代码完全一样。

## `Option<T>`和`Result<T>`的简要技术概述

在函数式编程术语中，这两个容器是[单子](https://dev.to/siy/beautiful-world-of-mondas-2cd6)。

`Option<T>`是`Option/Optional/Maybe`单子的简单实现。

`Result<T>`是`Either<L,R>`的有意简化和专门化版本:左类型是固定的，应该实现`Cause`接口。专门化使得 API 与`Option<T>`非常相似，以丧失通用性和通用性为代价，消除了大量不必要的键入。

这种特殊的实现集中在两件事上:

*   彼此之间以及与现有 JDK 类(如`Optional<T>`和`Stream<T>`)之间的互操作性
*   API 旨在明确表达意图

最后一句话值得更深入的解释。

每个容器都有几个*核心*方法:

*   工厂方法
*   `map()`转换方法，转换值但不改变*特殊状态* : present `Option<T>`保持存在，success `Result<T>`保持成功。
*   `flatMap()`转换方法，除了转换之外，还可以改变*特殊状态*:将当前`Option<T>`转换为空或者将成功`Result<T>`转换为失败。
*   `fold()`方法，一次处理两种情况(`Option<T>`存在/空，`Result<T>`成功/失败)。

除了*核心*方法，还有一堆*助手*方法，它们在经常观察到的用例中很有用。在这些方法中，有一组方法被明确设计为产生*副作用*。

`Option<T>`对*副作用*有以下方法:

```
Option<T> whenPresent(Consumer<? super T> consumer);
Option<T> whenEmpty(Runnable action);
Option<T> apply(Runnable emptyValConsumer, Consumer<? super T> nonEmptyValConsumer);
```

`Result<T>`对*副作用*有以下方法:

```
Result<T> onSuccess(Consumer<T> consumer);
Result<T> onSuccessDo(Runnable action);
Result<T> onFailure(Consumer<? super Cause> consumer);
Result<T> onFailureDo(Runnable action);
Result<T> apply(Consumer<? super Cause> failureConsumer, Consumer<? super T> successConsumer);
```

这些方法向读者暗示代码处理的是副作用而不是转换。

## 其他有用的工具

除了`Option<T>`和`Result<T>`，PFJ 还使用了其他一些通用类。下面，将详细介绍每一种方法。

## 功能

JDK 提供了许多有用的功能接口。不幸的是，通用函数的函数接口仅限于两个版本:单参数`Function<T, R>`和双参数`BiFunction<T, U, R>`。

显然，这在很多实际情况下是不够的。此外，由于某种原因，这些函数的类型参数与 Java 中函数的声明方式相反:结果类型列在最后，而在函数声明中，它是首先定义的。

PFJ 为具有 1 到 9 个参数的函数使用一组一致的函数接口。为简便起见，它们被称为`FN1`...`FN9`。到目前为止，还没有带有更多参数的函数的用例(通常这是一种代码味道)。但是，如果有必要的话，这个清单可以进一步扩大。

## 元组

元组是一种特殊的容器，可以用来在一个变量中存储不同类型的几个值。与类或记录不同，存储在内部的值没有名称。这使得它们成为在保留类型的同时捕获任意一组值的不可或缺的工具。这个用例的一个很好的例子是`Result.all()`和`Option.all()`组方法的实现。

在某种意义上，元组可以被认为是为函数调用准备的一组*冻结的参数*。从这个角度来看，让元组内部值只能通过`map()`方法访问的决定听起来很合理。然而，有两个参数的元组有额外的访问器，这使得使用`Tuple2<T1,T2>`作为各种`Pair<T1,T2>`实现的替代成为可能。

PFJ 使用具有 0 到 9 个值的一组一致的元组实现。提供具有 0 和 1 值的元组是为了一致性。

## 结论

实用函数式 Java 是一种基于函数式编程概念的现代、非常简洁但可读的 Java 编码风格。与传统的惯用 Java 编码风格相比，它提供了许多好处:

*   PFJ 使用 Java 编译器来帮助编写可靠的代码:
*   编译的代码通常是有效的
*   许多错误从运行时转移到了编译时
*   一些种类的错误，像`NullPointerException`或未处理的异常，实际上被消除了
*   PFJ 显著减少了与错误传播和处理相关的样板代码的数量，以及`null`检查
*   PFJ 专注于清晰地表达意图和减少精神开销