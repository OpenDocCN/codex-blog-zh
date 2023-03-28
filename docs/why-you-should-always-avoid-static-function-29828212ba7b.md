# 为什么你应该总是避免静态函数

> 原文：<https://medium.com/codex/why-you-should-always-avoid-static-function-29828212ba7b?source=collection_archive---------11----------------------->

写一个静态函数总是很诱人的。我们可以从任何地方直接调用它们，而不必注入/初始化/准备它们的包装类。但是我们不应该这样做。事实上，它们甚至不应该存在于任何编程语言中。

![](img/bc40f767cdbaa6674f3c5c24d2090b44.png)

# 1.它们是不可测试的

假设我们在类测试中有这个方法:

```
public void doSomething() {
  var a = 1;
  var b = 2;
  var c = MyClass.myStaticFunction(a, b);
  return a + b + c;
}
```

现在，我们的 *myStaticFunction* 调用数据库，执行一些验证，并用一些数据库结果对 *a* 和 *b* 进行难看的计算。但是我们想写一个单元测试来确保 *doSomething()* 总是返回:

```
a + b + c
```

我们需要初始化一个到数据库的真实连接，确保这个数据库填充了正确的数据，并在我们的单元测试中配置所有这些，只是为了确保… a + b + c

更好的选择是:

```
 public void doSomething(MyClass myClass) {
 var a = 1;
 var b = 2;
 var c = myClass.myFunction(a, b);
 return a + b + c;
}
```

那么我们可以像这样简单地编写我们的测试:

```
@Test
public void testDoSomething() {
  MyClass myClass = mock(myClass.class);
  doReturn(3).when(myClass).myFunction(1, 2);
  assertThat(myDoSomethingClass.doSomething(myClass)).isEqualTo(6);
}
```

用一个像 [mockito](https://site.mockito.org/) 这样的模拟框架来统一验证*做什么和*只做什么。

# 2.他们像瘟疫一样复制你的参数

如果一个人希望总是将相同的参数无用地传递给自己的函数，他会喜欢静态函数:

```
public void doSomeOtherThing(Database database, SqlUser sqlUser, SqlWhatever whatever) {
  var a = SqlDb.staticFunction(database, sqlUser, whatever, 't');
  var b = SqlDb.staticFunction(database, sqlUser, whatever, 'z');
  var c = SqlDb.staticFunction(database, sqlUser, whatever, 'f');
  var d = SqlDb.staticFunction(database, sqlUser, whatever, 'g');
}
```

如果你不像我一样喜欢它们，我们可以用上面的观点写同样的函数:

```
public void doSomeOtherThing(MyDb myDb) {
  var a = myDb.function('t');
  var b = myDb.function('z');
  var c = myDb.function('f');
  var d = myDb.function('g');
}
```

通过在前面的某个地方只初始化 MyDb 一次:

```
myDb = new MySqlDb(database, metadata, whatever);
```

# 3.他们不肯让步

假设有一种非常奇怪的情况，客户或我们的老板想要从 SQL 数据库转移到像 Mongo 这样的文档数据库(或者想要支持他们两个，wild wild ),而我们仍然有我们丑陋的静态函数:

```
public void doSomeOtherThing(Database database, SqlUser sqlUser, SqlWhatever whatever) {
  var a = SqlDb.staticFunction(database, sqlUser, whatever, 't');
  var b = SqlDb.staticFunction(database, sqlUser, whatever, 'z');
  var c = SqlDb.staticFunction(database, sqlUser, whatever, 'f');
  var d = SqlDb.staticFunction(database, sqlUser, whatever, 'g');
}
```

哦不！我们不认为除了 *sqlUser* 或类似 *SqlWhatever* 的其他用户类型之外还会有其他用户类型。

如果我们使用像 2 这样的非静态函数。如果不重复我们的参数，我们可以得到完全相同的代码，尽管:

```
public void doSomeOtherThing(MyDb myDb) {
  var a = myDb.function('t');
  var b = myDb.function('z');
  var c = myDb.function('f');
  var d = myDb.function('g');
}
```

使用先前在某处初始化的不同数据库:

```
myDb = new MyMongoDb(database, metadata, whatever);
```

# 更好的选择

假设我们的语言禁止使用静态函数，我们可以编写类似这样的代码来复制它的用法:

```
public class MyOtherClass {
  public void doSomething() {
    var a = 1;
    var b = 2;
    var c = new MyClass().myFunction(a, b);
    return a + b + c;
  }
}
```

这个丑:

```
new MyClass().myFunction(a, b);
```

然后可以用三种方式重构:
1。我们可以让 *myFunction* 只是 *MyOtherClass* 的一个方法；

2.如果 *new MyClass()。myFunction* 在其他地方被使用，我们可以传递依赖关系来清楚地表达它，就像我们之前做的那样:

```
public void doSomething(MyClass myClass) {
 var a = 1;
 var b = 2;
 var c = myClass.myFunction(a, b);
 return a + b + c;
}
```

3.或者，如果 *myClass.myFunction* 在同一个基类的子类中被调用，我们可以直接将 *myClass.myFunction* 向上移动到基类中(或者提取到一个新的基类中，如果该函数定义了一个真正的公共行为)。

# 结论

你应该总是避免静态函数，但是有时你仍然需要它们。在 Java 中，没有参数化的构造函数，你不能写两个相同类型的构造函数重载:

```
public class Test {
  public Test(String myFirstProperty) {
    this.myFirstProperty = myFirstProperty;
  }
  public Test(String mySecondProperty) {
    this.mySecondProperty = mySecondProperty;
  }
```

没有像 Python 中那样的命名参数的概念。但是您可以使用静态工厂函数来解决这个问题:

```
public static Test ofMyFirstProperty(String myFirstProperty) {
  Test test = new Test();
  test.myFirstProperty = myFirstProperty;
  return test;
}
```

或者使用一个构建器(它在背后使用一个静态函数)。除了这个和主入口点之外，我看不到任何其他合法的用法…