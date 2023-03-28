# 面向 Java 开发人员的 Kotlin 介绍—第 2 部分:类和对象

> 原文：<https://medium.com/codex/introduction-to-kotlin-for-java-developers-part-2-classes-and-objects-dbafe7cb177d?source=collection_archive---------11----------------------->

![](img/aa0852893e691143c84e1b1cd82266dc.png)

在这个系列的第二部分中，我将带您游览 Kotlin 中的类和对象。我将概述它们在哪些方面与 Java 不同。我希望你喜欢读这篇文章，就像我喜欢写它一样！如果你做到了，别忘了鼓掌:-)

该系列的其他部分:

*   [面向 Java 开发人员的 Kotlin 介绍—第 1 部分:函数和操作符](/codex/things-to-love-in-kotlin-coming-from-java-8-part-1-functions-and-operators-40aea17c8854)
*   [面向 Java 开发人员的 Kotlin 介绍—第 3 部分:泛型](/codex/introduction-to-kotlin-for-java-developers-part-3-generics-21e1646ec2ae)

# 1.构造器

当 Kotlin 第一次出现时，类只能有一个构造函数:主构造函数。就像 Kotlin 函数一样(不像 Java 函数)，它的参数可以有默认值，构造函数调用可以通过名称引用参数。这通常消除了在 Java 中定义重载的需要。在 98%的用例中，主构造函数就足够了。

二级构造函数在 2015 年晚些时候被添加到语言中(见发布说明[这里](https://blog.jetbrains.com/kotlin/2015/03/kotlin-m11-is-out/))，只是为了确保与需要多个构造函数的 Java 层次结构兼容(见规范[这里](https://github.com/JetBrains/kotlin/blob/master/spec-docs/secondary-constructors.md))。我不会在这篇文章中描述它们。如有必要，请参见此处的文档[和](https://kotlinlang.org/docs/classes.html#secondary-constructors)。尽可能使用有意义的名称来命名工厂函数，如这里的[所述。](https://kotlinlang.org/docs/coding-conventions.html#factory-functions)

主构造函数是通过在类名后面添加一个参数列表来定义的。类的主体可以有属性初始值设定项和初始值设定项块。两者都可以引用主构造函数参数，并且都在调用主构造函数时执行。任何以 var 的 val 为前缀的构造函数参数和任何属性初始值设定项都是属性定义。任何属性(无论是 val 还是 var，是否可为 null)都不能不初始化，否则会发生编译器错误。

示例:

[科特林文档](https://kotlinlang.org/docs/classes.html#constructors)

# 2.遗产

默认情况下，Kotlin 类是 final 类。只有标有`open`关键字的类是可继承的。继承是在类头中定义的，方法是在主构造函数定义后使用冒号，然后调用超类主构造函数。

示例:

方法不能被覆盖，除非用关键字`open`声明。覆盖超类方法的方法必须用`override`关键字声明。这提供了以下好处:

*   如果用`override`标记的方法实际上没有覆盖任何东西，编译器将产生一个错误。例如，这可能是因为超类中的一个 open 方法被重命名，而覆盖没有被重命名。
*   如果一个没有用`override`关键字标记的方法与一个超类方法同名，编译器将产生一个错误，从而隐藏它。这样，通过选择一个已经被超类中的一个方法使用的名字的意外重写就不会发生。

[科特林文档](https://kotlinlang.org/docs/inheritance.html)

# 3.性能

在 Java 中，属性是在类的每个实例中保留的存储位置，它保存一个值，可以通过名称引用。它通常与 getter 和 setter 方法成对出现。在 Java 中，getter 和 setter 方法必须显式写出。

在 Kotlin 中，属性是可以通过名称引用的 getter/setter 对。但是在 Java 中你可以写`obj.getProp()`，在 Kotlin 中你只需写`obj.prop`，同样的`obj.setProp(value)`变成了`obj.prop = value`。当使用`val`关键字定义属性时，它不能有 setter。属性可能对应于也可能不对应于存储位置。当它出现时，存储位置被称为“后备字段”。

在 Kotlin 中定义属性的语法如下:

当您没有显式指定它们时，Kotlin 会给您默认的 getters 和 setters，如下所示:

…其中`field`是指支持字段的关键字。当 getter 和 setter 都不引用支持字段时，Kotlin 不会创建一个。例如，该属性没有支持字段:

[科特林文档](https://kotlinlang.org/docs/properties.html)

## 3.1 委托属性

如果您发现自己一遍又一遍地编写相同的 getters 和 setters，您可能希望使用委托对象。语法如下:

委托对象必须实现`getValue`和一个`setValue`方法。科特林提供了一些有用的委托实现，比如`lazy`:

[科特林文档](https://kotlinlang.org/docs/delegated-properties.html)

## 3.2 属性和继承

因为在 Kotlin (getter / setter)中，属性基本上是一对函数，所以它可以被标记为抽象的，并且可以被覆盖，就像常规函数一样:

[科特林文档](https://kotlinlang.org/docs/inheritance.html#overriding-properties)

# 4.接口

在 Kotlin 中，接口就像一个抽象类，除了它不能保存状态(因为没有状态要初始化，所以接口不能有构造函数)。如果你对为什么没有状态感兴趣，看看这里的。你可以*声明属性，只要它们是抽象的或者没有支持字段(=状态)。也可以声明私有函数。*

接口允许多重继承，但是如果一个非私有函数是从多个超接口继承的，你必须明确当这个函数在子类的实例上被调用时该做什么。为此，您必须重写有问题的函数并实现它:

[科特林文档](https://kotlinlang.org/docs/interfaces.html)

# 5.密封类

密封类是这样一个类，它只能在声明到的同一个包和编译模块中被子类化。任何在其他地方声明的子类都会产生编译错误。这意味着所有可能的子类在编译时都是已知的。

下面是一个密封类及其子类的示例:

在`when`语句中使用密封类时，密封类的作用就显现出来了:

不仅如果覆盖了所有的情况，else 语句是不需要的，而且更重要的是，如果你添加了另一个子类，编译器将在没有覆盖新子类的每个`when`表达式上产生错误。

[科特林文档](https://kotlinlang.org/docs/sealed-classes.html)

# 6.目标

## 6.1 对象表达式

当涉及到匿名类时，Kotlin 没有与 Java 相同的语法。要在 Kotlin 中声明和实例化匿名类，请使用对象表达式:

在 Kotlin 中创建接口或抽象类的一次性实现时，对象表达式非常方便。

就像匿名函数一样，对象表达式可以从封闭范围访问变量，从而创建闭包:

[科特林文档](https://kotlinlang.org/docs/object-declarations.html#object-expressions)

## 6.2 对象声明

对象不一定是匿名的，它们也可以被命名，因此定义了单例:

你可以直接用名字来引用一个对象:`DataProviderManager.registerDataProvider(...)`

[科特林文档](https://kotlinlang.org/docs/object-declarations.html#object-declarations-overview)

## 6.3 伴随对象

Kotlin 没有静态字段和方法。相反，它有伴侣对象。

当一个对象在一个类中被声明时，它可以用关键字`companion`来标记。这些对象的成员可以简单地用类名来引用:

[科特林文档](https://kotlinlang.org/docs/object-declarations.html#companion-objects)

# 7.对象析构

一个对象可以在赋值时分解成它的组成部分，如下所示:

要做到这一点，只需提供`component1...N`函数并将它们标记为操作符。

[科特林文档](https://kotlinlang.org/docs/destructuring-declarations.html)

# 8.数据类别

当一个类用关键字`data`标记时，Kotlin 会根据主构造函数中声明的属性自动生成以下函数(仅在主构造函数中) :

*   `equals()` / `hashCode()`
*   `toString()`的形式`"User(name=John, age=42)"`
*   `component1...N`功能对应于其声明顺序中的属性
*   `copy()`功能

如果这些自动生成的实现之一不符合您的需要，只需定义您自己的实现，它将优先于生成的实现。

[科特林文档](https://kotlinlang.org/docs/data-classes.html)

# 资源

[科特林文档](https://kotlinlang.org/docs/home.html)

[科特林游乐场](https://play.kotlinlang.org/)

[科特林公案](https://play.kotlinlang.org/koans/overview)