# Ruby 类、对象和方法

> 原文：<https://medium.com/codex/ruby-classes-objects-methods-4568b9f443d7?source=collection_archive---------6----------------------->

在 Ruby 中，类、对象和方法是极其重要的概念，即使你可能已经(也可能没有)在使用它们，为了真正理解你的代码是如何工作的*在幕后*掌握 Ruby 语言的这些基础组件是至关重要的。在这篇文章中，我将介绍这些是什么，以及如何创建和使用它们。

让我们开始吧

# Ruby 类

Ruby 是一种面向对象的语言，这意味着它依赖于类和对象的概念来将程序构建成简单且可重用的代码片段，这些代码片段充当创建对象的单个*实例*的蓝图。Ruby 类是蓝图。在创建自定义类时，您正在创建一个新的数据类型。

作为一名程序员，你构建这些类来代表现实世界的实体(例如代表*所有*书籍的 Book 类)，该类被用作创建该类的*实例*的规则集(或蓝图)(例如单行本)。该类定义了该类的实例将具有哪些属性(例如一本书将有标题、作者、描述和评级属性)，但它不决定这些属性的*值*将是什么，类决定一个对象的实例能够做什么(例如。可以添加或删除一本书)。

要创建新的类，可以使用以下语法:

```
**class** Book
**end**
```

为了定义所有书籍应该具有的属性，您需要在类中提供它们。我们不打算在这篇文章中讨论迁移和数据库，所以为了定义所有书籍的属性，我们将使用 **attr_accessor** 。 **Attr_accessor** 创建了两个方法(而不是在类中手工编写它们)—**getter 方法**和 **setter 方法**。 **getter 方法**用于*检索*一个属性的值，而 **setter 方法**用于*给*一个属性赋值。同样，这两个都是在使用 **attr_accessor** 时在幕后创建的:

```
**class** Book
   attr_accessor :title, :author, :description, :rating
**end**
```

因此，对于 Book 类的当前迭代，Book 的所有实例都需要有标题、作者、描述和评级。

# Ruby 对象

![](img/7ba3ccb7824bfc90cee79c5f859fb902.png)

Books 类的对象！

如上所述，类(例如。Book 类)被用作创建该类的*实例*的蓝图。对象是一个类的单个实例(例如。单行本)。在 Ruby 语言中，一切都是对象(除了条件、块和消息)——所以类也是对象。

以书籍为例——用面向对象的术语来说，每本书是被称为 Book 的对象类的一个实例。在我们的例子中，一本书由标题*、作者、描述*和*评级*组成——这些是 Book 类的属性，当 Book 类的实例被创建时，与这些属性相对应的值(数据)将被赋值。

要创建一个新的 Book 对象，您可以在该类上使用 Ruby 的 *new* **方法**，并为该类的已定义属性提供值:

```
**first_book = Book.new()
first_book.title = "Really Great Book"
first_book.author = "Denali Balser" 
first_book.description "best book ever"
first_book.rating = 100**
```

上面我们创建了 Book 类的一个新实例，也就是一个对象，并将其分配给一个变量( **first_book** )，然后使用 Ruby 的点符号将值分配给该实例的属性。为了访问该对象的属性，使用以下语法:

```
**puts first_book.title #=> Really Great Book**
```

# Ruby 方法

一本书除了创建自己的实例之外，还可能有其他功能。这就是方法发挥作用的地方。Ruby 方法将一个或多个可重复的语句捆绑成一个单元。方法是在一个对象上定义的，也就是在一个类中定义的——一个类可以由特征(属性)和功能(方法)的组合组成。

按照 Ruby 惯例，方法名应该以小写字母开头，如果以大写字母开头，可能会被解释为常量，调用可能会被错误地解析。此外，Ruby 方法必须在被调用之前定义，否则将引发未定义方法调用的异常。

例如，Book 类可能有一个名为 **change_author** 的方法，该方法更改 Book 实例的 **author** 属性。这个方法将在 Book 类中定义，因为它特定于这个类。

创建方法的语法:

```
**def change_author 
    #method body (code for changing the author)
end**
```

方法也可以接受参数；在我们的示例方法中，您可能希望提供新作者的姓名，以便替换现有的作者。为此，您需要编写带有参数的方法:

```
**class Book 
   attr_accessor :title, :author, :description, :rating**#define change_author method to accept arguments:
   **def change_author(obj, new_author) 
     obj.author = new_author
   end****end**#create new Book object and assign to first_book variable:
**first_book = Book.new()
first_book.title = "Really Great Book"
first_book.author = "Denali Balser" 
first_book.description "best book ever"
first_book.rating = 100**#call change_author method on first_book object with obj and new_author arguments:
**first_book.change_author(first_book, "J. R. R. Tolkien")**#display the author of first_book object in console:
**puts first_book.author #=> J. R. R. Tolkien**
```

现在，当您调用 **change_author** 方法时，您将提供您想要更改的对象( **obj** )和 **new_author** 参数，它们将在方法体中用于更新图书实例的 **author** 属性。

# 现在都在一起

概括地说，类定义了该类的一个实例——或单个对象——将会是什么样子(就像一个蓝图),一个实例就是一个对象，而方法(在类中定义)描述了该类/对象的一个实例将能够做什么。