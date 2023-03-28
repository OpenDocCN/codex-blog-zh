# Kotlin 的数据类和 Java 16 记录的区别

> 原文：<https://medium.com/codex/the-difference-between-kotlins-data-classes-and-java-16-records-7e25fdbcb50d?source=collection_archive---------5----------------------->

## 详细的外观

![](img/fa17be42e9ada47edf9cf7840de0d1c4.png)

[史蒂夫·哈维](https://unsplash.com/@trommelkopf?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

当 Java 第一次在 Java 14 中引入记录作为预览，后来在 Java 16 中发布时，它让许多人想起了 Kotlin 的数据类。虽然它们以非常相似的方式解决相同的问题，但在处理方式上有一些不同。

我将按照最相关到更边缘的顺序大致列出它们。我试图找出所有存在的差异，但如果我错过了什么，请随意评论。

# 例子&为什么它们很重要

在讨论差异之前，我想先举几个例子。如果你已经知道它们是如何工作的，你可以跳过这一节。

Java 的记录使用了新的关键字`record`:

```
record Name(String firstName, String lastName) { }
```

同时，Kotlin 的数据类在类前使用修饰符`data`:

```
data class Name(val firstName: String, val lastName: String)
```

两者都自动创建了大部分样板代码，否则你必须创建:getters、setters、`equals`、`hashcode`和`toString`。

这不仅有助于减少样板代码，还可以使重构更加安全，因为现在您不必记得向自动生成的方法添加新的字段。

# 最终课程

记录和数据类都将编译成最终类。对于 Kotlin，这是所有类的默认设置，但是数据类不能被声明`open`。

# 可变成员

Java 的记录是完全不可变的。然而，对于数据类，您可以指定哪些字段是可变的，哪些是不可变的(通常使用`var` vs `val`关键字)。例如，在下面的代码中，`id`不能被重新分配，但`name`可以:

```
record Person(val id: Int, var name: Name)
```

在不可变字段的情况下，它会在结果代码中将它们标记为`final`。Java 的记录总是有`final`字段。

# 非构造函数字段

记录不允许您定义额外的实例字段，只能定义静态字段。但是，使用数据类，您可以。当然，这些并不包括在自动生成的方法中，但是对于一些迫切需要创建的值是有用的:

```
data class Name(val firstName: String, val lastName: String) {
    val fullName = firstName + ' ' + lastName
}
```

# java.lang.Record

Java 的记录现在总是实现新的类`[java.lang.Record](https://download.java.net/java/early_access/jdk17/docs/api/java.base/java/lang/Record.html)`。这个抽象类本身没有添加任何有趣的方法，但是可以用来捕捉所有记录。

这个超类也意味着**记录不能扩展其他类**——尽管它们仍然可以实现接口。数据类没有这种限制。

# 解构

Kotlin 已经支持数据类的析构。对于 Java，[目前有一个关于](https://cr.openjdk.java.net/~briangoetz/amber/serialization.html#sidebar-pattern-matching)的提议，但是目前的 Java 版本(包括 Java 17)都不支持这个语法。

## 科特林的毁灭

Kotlin 的赋值语法中内置了析构。给定之前的`Person`类，我现在可以做以下事情:

```
val (id, name) = Person(0, Name("Oscar", "Ablinger"))
```

这将利用另一组自动生成的方法编译成两个不同的赋值语句。它本质上是以下两个语句的语法糖:

```
val id = person.component1()
val name = person.component2()
```

这两个变量都是类型化的，因此当更改字段类型时，结果变量将更新它们的类型。它们也不必是详尽的，所以当您添加另一个字段时，相同的代码仍然可以工作。

然而，这意味着如果您添加一个字段作为除最后一个元素之外的任何元素，并且它与它替换的字段共享类型，**代码将编译并且不会给出错误！这是一个很难追踪的错误来源。**

# toString

虽然两者都获得了自动生成的`toString`方法，但 Java 和 Kotlin 的结果看起来略有不同。Java 将生成以下字符串:

```
record Name(String firstName, String lastName) { }new Name("Oscar", "Ablinger").toString();
// Name[firstName=Oscar, lastName=Ablinger]
```

Kotlin 生成一个类似的字符串:

```
data class Name(val firstName: String, val lastName: String)Name("Oscar", "Ablinger").toString()
// Name(firstName=Oscar, lastName=Ablinger)
```

如您所见，唯一真正的区别是 Java 使用方括号，而 Kotlin 使用圆括号。令人恼火的是，IntelliJ 为类生成的默认`toString`使用了大括号。

您也可以使用这个生成的字符串作为`toString`方法的样式指南的基础。

# 反射

数据类本质上只是普通类的语法糖。因此，一旦被编译，很难将它们与其他类区分开来。

然而，使用反射可以很容易地识别记录。不仅仅是因为它们总是实现`java.lang.Record`类型，还因为`Class`中增加了两个新方法:`[isRecord](https://download.java.net/java/early_access/jdk17/docs/api/java.base/java/lang/Class.html#isRecord())`和`[getRecordComponents](https://download.java.net/java/early_access/jdk17/docs/api/java.base/java/lang/Class.html#getRecordComponents())`。

如果该类是一个记录，`isRecord`简单地返回`true`，否则返回`false`。

`getRecordComponents`返回记录中所有字段的信息，如果不是记录，则返回`null`。术语“组件”只是用来指记录的非静态字段。使用它，您可以访问与方法相同的信息:注释、名称、类型等等。

# 吸气剂

Kotlin 在带有`get`前缀的知名模式之后生成 getters。记录现在去掉了这个前缀，取而代之的是将方法命名为与字段相同的名称。

# 履行

我已经写了关于`toString`方法的不同结果。此外，所有方法的实现方式也不同。

Kotlin 编译器只是为每个方法生成代码，而 Java 将实际的代码外包给一个静态方法。这些方法的实现只需调用带有必要信息的`[ObjectMethods.bootstrap](https://download.java.net/java/early_access/jdk17/docs/api/java.base/java/lang/runtime/ObjectMethods.html#bootstrap(java.lang.invoke.MethodHandles.Lookup,java.lang.String,java.lang.invoke.TypeDescriptor,java.lang.Class,java.lang.String,java.lang.invoke.MethodHandle...))`方法。我假设这样做是为了尽可能减少记录的字节大小。

# 结论

最后，数据类和记录在用法上非常相似。虽然数据类只是简单的语法糖，但是记录为 Java 标准库增加了很多。尽管如此，数据类确实允许比记录更多的功能。这是否是一件严格意义上的好事是另一个争论。