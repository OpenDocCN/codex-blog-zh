# 理解 Java 函数接口

> 原文：<https://medium.com/codex/understanding-java-functional-interfaces-d028b04c15d0?source=collection_archive---------7----------------------->

## 下面是功能接口的类型和细节。

![](img/4ede88a375d2dafb8a8b39c50a4968c6.png)

由[基利安·卡蒂涅斯](https://unsplash.com/@kikisad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

当 lambda
表达式或方法引用作为参数传递给方法时，函数接口是用于参数的类型。

## 兰姆达斯是什么？

Lambdas 是简洁表达的单一方法类，表示 Java 8 中引入的行为。它们既可以赋给变量，也可以传递给其他方法，就像我们将数据作为参数传递一样。

```
// Concatenating strings
(String s1, String s2) -> s1+s2;
```

就像普通的 Java 方法一样，lambda 表达式有可选的输入参数、主体和可选的返回值。

```
input-arguments -> body
```

## 拉姆达类型

你可能想知道这些表达式的类型是什么。由于 Java 是一种强类型语言，通常必须声明类型；否则，编译器会嘲笑我们。然而，我们在声明上述 lambda 表达式时省略了类型。那么，lambda 表达式的类型是什么？它是字符串、对象还是新的函数类型？任何 lambda 的类型都是一个*功能接口。*

## 界面与功能界面

接口是没有实现所有方法的类，所以使用接口的人必须实现逻辑。随着 Java 8 的引入，这个定义已经改变，现在接口可以将实现逻辑作为默认方法，也就是说，我们可以给出一个默认的实现，其他人可以在需要时覆盖它们。Java 8 还引入了另一种拥有未实现方法的方式，称为静态方法。

Functional Interface 是 Java 接口的变体，其中只允许一个未实现的(抽象)方法，而允许任意数量的默认和静态方法。可选地，它可以用可选的`@FunctionalInterface`注释来修饰。这允许编译器在带注释的接口不满足条件时生成错误。

## 样品

让我们创建一个如下的功能界面:

```
@FunctionalInterface
interface IAddable<T> {
    *// To add two objects* public T add(T t1, T t2);
}
```

现在我们可以使用它了

```
IAddable<String> iAddable = (t1, t2) -> t1+" "+t2+"!";
System.*out*.println(iAddable.add("Hi","There"));

IAddable<Integer> integerIAddable = (t1, t2) -> t1+t2;
System.*out*.println(integerIAddable.add(3,5));//Output
//Hi There !
//8
```

重要的是，现在您可以从类实现中移除业务逻辑，并作为参数传递。

## 匿名类

如果我们不使用 lambda，事情会变得多么混乱。技术垃圾遮蔽了业务逻辑。

```
IAddable<String> stringIAddable = new IAddable<String>() {
    @Override
    public String add(String t1, String t2) {
        return t1+t2;
    }
};
```

## Lambda 表达式与内部/匿名类

当我们使用内部类时，它创建了一个新的作用域。可以通过实例化同名的新局部变量来隐藏封闭范围内的局部变量。关键字 ***这个*** 在我们的内部类里面可以作为对它的实例的引用。

然而，Lambda 表达式使用封闭范围。无法隐藏 lambda 主体内封闭范围的变量。在这种情况下，关键字 ***this*** 是对封闭实例的引用。

## Java 现在是智能的

即使我们没有用 lambda 发送任何类型的信息，java 也会根据分配的变量识别类型，下面是一个字符串而不是整数。

```
IAddable<String> iAddable = (t1, t2) -> t1+" "+t2+"!";
```

输入类型将由左边的类型决定，方法的返回类型由方法决定。

# 内置功能接口

你可能认为我们需要一个新的函数类型来表示这种表达式。取而代之的是，Java 设计者巧妙地利用现有的接口和一个单一的抽象方法作为 lambda 的类型，这就是所谓的函数接口。并将它们存储在一个名为`java.util.function`的库中

Java 定义了许多类型的函数接口，在这里阅读:

 [## java.util.function (Java 平台 SE 8)

### 函数接口为 lambda 表达式和方法引用提供目标类型。每个功能界面都有…

docs.oracle.com](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html) 

Java 函数接口是一个标准，在这里你可以定义你自己的函数接口。需要时，您可以使用自己的功能接口。

## 关键功能界面

java 提供的函数接口对我们来说很少是重要的。让我们看一看。

## 1.述语

检查条件并返回布尔值。输入可以是任何类型。

```
@FunctionalInterface
public interface Predicate<T> {
  boolean test(T t);
}
```

我们可以用这个来检查一个字符串是空的还是不是这样:

```
Predicate<String> stringPredicate= s -> s.isEmpty();
System.*out*.println(stringPredicate.test("nuwan"));
//Output: false
```

## 2.功能

Function 接口表示接受单个参数并返回单个值的函数(方法)。这将接受类型 T 并返回类型 r。可以在方法内进行数据转换。

```
@FunctionalInterface
public interface Function<T, R> {
  R apply(T t);
}
```

下面将把一个整数转换成一个字符串。

```
Function<String,Integer> fun = s -> Integer.*valueOf*(s);
System.*out*.println(fun.apply("0001"));
//Output: 1
```

## 3.消费者

使用者接受单个参数，但不返回任何结果:

```
@FunctionalInterface
public interface Consumer<T> {
  void accept(T t);
}
```

例子

```
Consumer<String> consumer = o -> System.*out*.println(o);
consumer.accept("Use consumer interface");
```

## 4.供应者

不使用输入参数返回某些内容。

```
@FunctionalInterface
public interface Supplier<T> {
  T get();
}
```

例子

```
Supplier<String> supplier = () ->  "Return Something";
System.*out*.println(supplier.get());
```

## 5.一元运算符

这采用单个参数并返回相同类型的参数。

```
UnaryOperator<String> uo = s -> s.toUpperCase();
System.*out*.println(uo.apply("send lowercase"));
//Output: SEND LOWERCASE
```

## 6.二元运算符

接受两个参数并返回一个值。参数和返回类型必须是同一类型。

```
BinaryOperator<String> bo = (s, s2) -> s+" - "+s2;
System.*out*.println(bo.apply("Hi","There"));
//Output: Hi - There
```

## 两个自变量函数

我们上面讨论的函数是单输入或无参数函数，如果我们要使用两个参数作为输入，我们可以使用

*   双预测
*   双消费者
*   双功能

```
@FunctionalInterface
public interface BiFunction<T, U, R> {
  R apply(T t, U u);
}
```

## 原始函数专门化

由于基元类型不能是泛型类型实参，所以对于最常用的基元类型 *double* 、 *int* 、 *long* ，以及它们在实参和返回类型中的组合，都有不同版本的*函数*接口。一般来说，建议使用更专业的形式来避免自动装箱。例如，`IntFunction<Foo>`应该优先于`Function<Integer, Foo>`。

## 传统功能接口

Java 以前版本的许多接口都符合 a `*FunctionalInterface*`的约束，我们可以把它们当作 lambdas 来使用。突出的例子包括在并发 API 中使用的`*Runnable*`和`*Callable*`接口。

## 虚拟(默认)方法

虽然 lambdas 被添加到语言中很好，但是如果它们不能用于现有的 API，那么使用它们就没有意义了。为了支持将 lambdas 吸收到我们的库中，接口需要发展。也就是说，我们需要能够向已经发布的 API 添加额外的功能。

在 Java 8 之前，接口是抽象的，你当然不能给它添加实现。然而，Java 8 对此进行了改造，称之为虚拟方法。collections API 就是这样一个例子，其中将 lambdas 引入到等式中彻底改变并增强了 API。

由于 Java 支持接口多重继承，相同的缺省方法可能会出现不止一次，在这种情况下，编译器会有麻烦，开发人员需要提供他们自己的实现来解决这个问题。

## 方法引用

方法引用是调用现有方法的快捷方式。

```
Consumer<String> consumer = **o -> System.*out*.println(o)**;
consumer.accept("Use consumer interface");
```

上面的 lambda 表达式可以使用方法引用重写如下。

```
Consumer<String> stringConsumer = **System.*out*::println**;
consumer.accept("Use consumer interface");
```

## 目标类型

函数接口可以在多种上下文中提供目标类型，如赋值上下文、方法调用或强制转换上下文:

```
 // Assignment context
     Predicate<String> p = String::isEmpty;

     // Method invocation context
     stream.filter(e -> e.getSize() > 10)...

     // Cast context
     stream.map((ToIntFunction) e -> e.getSize())...
```

## 模式和最佳实践

开发者在创建新的功能接口之前，应该先探索[**Java . util . function**](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/function/package-summary.html)包。

使用 [@FunctionalInterface](http://twitter.com/FunctionalInterface) 注释来避免对接口的意外更改，以免违反功能接口标准。

向接口添加太多的默认方法不是一个很好的架构决策，用相同的默认方法扩展不同的功能接口可能会有问题。

避免重载以函数接口为参数的方法，要使用不同名称的方法，避免冲突。

保持 Lambda 表达式简短且不言自明，使用单行结构而不是一大段代码。

避免为 Lambda 指定参数类型，在大多数情况下，编译器能够解析类型。

```
//Use this 
(a, b) -> a.toLowerCase() + b.toLowerCase();
//instead of this
(String a, String b) -> a.toLowerCase() + b.toLowerCase();
```

避免在单个参数周围使用括号

```
a -> a.toLowerCase();//YES
(a) -> a.toLowerCase();//NO
```

避免 Return 语句和大括号

```
a -> a.toLowerCase();//Yes
a -> {return a.toLowerCase()};//No
```

尽可能使用方法引用

```
a -> a.toLowerCase();//No
String::toLowerCase;//Yes
```

## 有效最终

对有效最终变量的限制禁止了对动态变化的局部变量的访问，这些变量的捕获可能会引入并发问题。

使用“有效的 Final”变量，否则会导致编译时错误。这种方法使得 lambda 的执行是线程安全的。

即使变量不能改变，但是里面的变量可以改变，所以用 Lambdas 做并行计算的时候

```
//compile time error
public void method() {
    String localVariable = "Local";
    Foo foo = parameter -> {
        String localVariable = parameter;
        return localVariable;
    };
}
```

但是这是可能的

```
int[] total = new int[1];
Runnable r = () -> total[0]++;
r.run();
```

参考:

[https://www . oreilly . com/content/whats-new-in-Java-8-lambdas/](https://www.oreilly.com/content/whats-new-in-java-8-lambdas/)
[https://www . oreilly . com/content/Java-8-functional-interfaces/](https://www.oreilly.com/content/java-8-functional-interfaces/)
[https://docs . Oracle . com/javase/8/docs/API/Java/util/function/package-summary . html](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)
[https://www.baeldung.com/java-8-lambda-expressions-tips](https://www.baeldung.com/java-8-lambda-expressions-tips)