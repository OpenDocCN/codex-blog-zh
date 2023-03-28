# 与 Java 分手后如何与 Scala 开始新的关系

> 原文：<https://medium.com/codex/how-to-start-a-new-relationship-with-scala-after-breaking-up-with-java-72d993017cc3?source=collection_archive---------10----------------------->

![](img/03cf7e493b6511b55a0fcab361d4a7c6.png)

由[马库斯·斯皮斯克](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在从事 Java 工作 10 多年后，我最近开始在新岗位上从事 Scala 工作。这个转变一点也不容易。

这就是为什么，这个想法让我想到我应该分享我的学习，这样其他人就可以从中受益，而不会遇到我所面临的问题。

事实上，很多时候我们需要做一些项目，这些项目的技术与我们的专业知识有一点点不同。java 开发人员经常需要从事一些 Scala 项目，这似乎很常见，可能是在公司内部，也可能是在换工作的时候。由于 Scala 是在 Java 之上创建的，所以很容易(几乎)切换。但是如果我们注意到，从面向对象编程到函数式编程有一个全新的编程范式转变。在这篇文章中，我将讨论 Scala 的棘手之处，或者我会说是函数式编程。

# 基本差异

在深入研究这门语言的复杂性之前，我们应该了解两者之间的基本区别。根据定义:

**函数式编程**是一种一切围绕函数的编程技术。这意味着，对于要执行的任何类型的任务，我们将只创建函数。

另一方面，**面向对象编程**范式是完全基于对象的，在那里运行任何任务，你都会创建一个对象。

函数式编程(FP)原则:

♀**纯函数**是函数式编程的精髓。这些函数的输出将保持不变，即使你调用它们无数次。换句话说，外态对纯函数没有影响，或者你可以说不会有副作用。例如:函数`getSum(a , b)`将返回输入的总和，即`a+b`，并且将始终产生与输出相同的总和。

不变性是 FP 提供的另一大优势。由于这与纯函数直接相关，所以我们总是会创建不可变的对象。与默认创建可变对象的 OOP 相比，它提高了应用程序的性能。

并行性在 FP 中很容易实现，因为每个表达式都是引用透明的。例如，如果 x，y 和& &是引用透明的，那么 x & & y 将会执行，不管执行多少次，对它们都没有(副作用),因此这将使它们并行化变得很容易。

让我们来谈谈 Scala 新手在学习时需要记住的一些要点:

# 一切都是表达

在 Scala 中，一切都是表达式，不像 Java，在 Java 中你有一个不会返回任何东西的代码块。比如:Java 中的“if else”块只是一个块，不会返回任何东西。

```
if(isThisReallyTrue) // Please do somethingelse //don’t do anything
```

但在 Scala 中,“if else”块本身就是一个表达式，将返回某种值。例如:

```
if(matchRegex) str = “abc”else str = “”
```

在上面的例子中，你可以将结果存储到一个 val 中并使用它。

```
val x: String = if(matchRegex) str = “abc” else str = “”
```

# Scala 中的纯函数

让我们看看如何在 Scala 中定义纯函数:

```
def toUpper(str:String) :String = str.toUpperCase()
```

上面的方法会将小写字符串转换成大写。因此，无论你调用这个函数多少次，输出都不会修改字符串。

**引用透明性**是一个表达式，如果它可以被它的值替换并且不改变它的行为。这是纯函数的一个重要性质，因为函数`f`只有在`f(x)`的表达式对于 x 的每个值都是参照透明的情况下才是纯的。

就拿这个表达式来说:`arg + (4–2)`是指称透明的，因为它可以用`arg + 2`来代替。

这在性能方面起着重要的作用，因为表达式是在编译时计算的，在运行时可以节省几个周期。

# Scala 如何实现不变性

Scala 更喜欢不可变的代码，这可能会迫使程序员思考以不可变的方式编写代码。默认情况下，Scala 中的集合是不可变的。用 Scala 的函数式编程写的一句名言:

> 首选 val、不可变对象和没有副作用的方法。先伸手去拿。当你有特定的需要和理由时，使用变量、可变对象和有副作用的方法。

让我们来谈谈 Java，如果你需要让你的变量不可变，那么你需要让它成为最终变量。一个 java 方法会这样写:

```
public void doSomething(Person person) { // TODO something}
```

现在，您可以将 Person 作为最终结果，如下所示:

```
public void doSomething(final Person person)
```

但是在 Scala 中，如果你只是传递参数，默认情况下它是 val，相当于 final。在 Scala 中，你可以这样定义方法:

```
def doSomething(person: Person) : Unit = // TODO something
```

默认情况下，这个 person 对象是有值。

让我们来谈谈集合以及如何在 Scala 中操作它。

默认情况下，Scala 的集合类如`Set, Map, List, Seq`是不可变的，而在 Java 中它们是可变的。创建 any 集合类的新实例时，不能直接修改它。

有一个常见的场景，几乎每个人都在他们的项目中使用它来动态更新定制对象的列表。

例如，创建一个`List<Person> objects`

在 Java 中，我们会这样做:

```
List<Person> personList = new ArrayList<>();for(x: someOtherCollectionObject){
  Person p = new Person();
   p.setName(x.name);
   personList.add(p);}
```

但是你不能在 Scala 中使用相同的语法，因为它是一个不可变的列表。虽然，您也可以显式地创建可变列表并实现上面的语法(以 Scala 的方式)，但是您会发现可变包的使用在项目中是禁用的，并且是非常不鼓励的。

这可以通过功能图来解决。现在，Scala 中的每个集合都有三个基本方法:`map, flatmap, filter`这是极其有用的。

因此，新的语法将是:

```
Val personList: List<Person> = someOtherCollectionObject.map( ele => {
  Person( name = obj.name)
})
```

从上面的例子中你可以看到，在 Scala 中，for(或 while)循环的使用是必须的，也是不鼓励的。Scala(函数式编程)将为不同的用例提供不同的方法，这有助于消除像(索引越界等)这样的[差一个的错误](https://en.wikipedia.org/wiki/Off-by-one_error)。

# Scala 中的并发性

Scala 为并发性提供了极好的支持。上面提到的两点(纯度、不变性)是实现健壮并发的关键。让我们看看如何:

由于集合的不变性，在多个线程之间共享数据是不可能的，程序员不必担心并发问题。

不像在 java 中，如果你需要解决这个问题，你需要付出很多努力来实现它(线程同步，线程安全收集)。

**期货**就像在后台做任何任务一样。一个简单的未来可以这样写:

```
val future = Future (println(“Print first future”))
```

现在这个未来将被立即解决，这不会使它变得纯粹，因为它有副作用。因此，为了使它更纯粹，我们可以使用一个名为 Cats 的库，它提供了一个名为 IO Monad 的具体工具，该工具将使这种未来懒惰得到评估，并且根据需要，您可以解决它。所以，用木卫一来概括:

```
for {
_ <- IO.fromFuture(IO(
    Future(println(“Print first future”)))
} yield{}
```

Scala 中还有其他选项可以实现并发。但是讨论超出了本文的范围。

## 结论

总之，我提到的这些提示只是冰山一角，在海洋深处还有更多值得挖掘的地方。但是以上几点在你的日常开发中使用频率非常高。Scala 必须提供更强大的特性，比如模式匹配、自然的代码编写方式(+，-可以是有效的函数名)、更面向数学的风格等等。

**免责声明**:作为一个 Scala 新手，我已经尽我所知写了这些指针。请随时分享您的专家建议和意见。

如果你喜欢这篇文章，请为它鼓掌。随着我在 Scala 上的进一步工作，这将激励我分享更多的经验。