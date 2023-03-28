# 学习 Python 中的面向对象编程

> 原文：<https://medium.com/codex/object-oriented-programming-in-python-for-beginners-89b72552d6b0?source=collection_archive---------0----------------------->

![](img/97acf1088c0b2cb261204d863e6ce86c.png)

照片由[替代代码](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## [法典](http://medium.com/codex)

# 介绍

Python 在 2020 年仍然被认为是顶级语言之一，许多人仍然在使用这种语言。主要是因为 Python 可以被认为是一种随意且容易打字的语言。

您可以编写 Python 脚本来实现许多目的，如启动服务器、创建 API 端点、使用 Python 的各种 web 框架、数据科学、机器学习等等来构建网站。

在这篇文章中，我们将讨论 Python 中的面向对象编程。我们将会看到在 Python 中我们能在多大程度上使用 OOP。如果你是面向对象编程的新手，欢迎查看我在 PHP 中的 [**面向对象编程的帖子**](https://rasyue.com/programming/object-oriented-programming-in-php-for-beginners/) 来帮助你理解 OOP 的基础。

# Python OOP 中的类、对象和构造函数

所以，我们开始工作吧。首先，让我们试着用 Python 创建一个类，并创建它的一个实例，我们称之为对象。

```
**import** datetime**class** Rasyue:**def** __init__(self, post_name, post_author, post_date, blog_name):self.post_name = post_nameself.post_author = post_authorself.post_date = post_dateself.blog_name = blog_name**def** postdetails(self):date_time =  self.post_date.strftime("%Y/%m/%d")print("The title of this post is "+self.post_name+". It was written by "+self.post_author+" on "+date_time+" on "+self.blog_name)rasyue = Rasyue("Object-Oriented Programming in Python","Rasyue",datetime.date(2020, 7, 1), # year, month, day"Rasyue.com")rasyue.postdetails()
```

好的，我们上面写的很基本的东西。为了帮助你更好地理解上面的内容，我们首先创建一个类名`***Rasyue***`，然后在这个类中，我们定义了构造函数`***__init__***`。

但是，你可能会问，那`***self***`呢？

# `**self**`

嗯，`***self***`指的是这个类本身。关键字可以是任何东西，不一定非得是`**self**`。你可以用任何你喜欢的关键字替换它，并以同样的方式使用它。当我说它指的是类本身时，意思是如果你试图使用类本身的属性和方法，你必须使用`**self**`。请参见下面的示例。

```
**import** datetime**class** Rasyue:**def** __init__(self, post_name, post_author, post_date, blog_name):self.post_name = post_nameself.post_author = post_authorself.post_date = post_dateself.blog_name = blog_name**def** printDetails(self):print("My blog is "+self.blog_name)**def** postdetails(self):date_time =  self.post_date.strftime("%Y/%m/%d")print("The title of this post is "+self.post_name+". It was written by "+self.post_author+" on "+date_time+" on "+self.blog_name)self.printDetails()rasyue = Rasyue("Object-Oriented Programming in Python","Rasyue",datetime.date(2020, 7, 1), # year, month, day"Rasyue.com")rasyue.postdetails()
```

# 遗产

现在，让我们看看 Python 中的继承是如何工作的。继承是 OOP 中允许一个类继承另一个类的另一个方面。父子关系。这允许子类使用父类的任何方法或属性。

```
**import** datetime#creates the Parent Class first**class** RasyueParent:**def** __init__(self):self.post_name = "Object Oriented Programming in Python"self.blog_name = "Rasyue.com"**def** postdetails(self):print("The title of this post is "+self.post_name+". Written on "+self.blog_name)#creates the Child Class that inherits the Parent Class RasyueParent**class** RasyueChild(RasyueParent):**def** childpostdetails(self):print("This is from the Child Class")r = RasyueChild()r.childpostdetails()r.postdetails()
```

# Python 中的方法覆盖

方法重写是指子类有一个方法重写父类的方法。通常方法名称是相同的。参考下面的代码。

```
**import** datetime#creates the Parent Class first**class** RasyueParent:**def** __init__(self):self.post_name = "Object Oriented Programming in Python"self.blog_name = "Rasyue.com"**def** postdetails(self):print("The title of this post is "+self.post_name+". Written on "+self.blog_name)#creates the Child Class that inherits the Parent Class RasyueParent**class** RasyueChild(RasyueParent):**def** postdetails(self):print("This is from the Child Class")r = RasyueChild()r.postdetails()
```

当您运行上面的脚本时，它将打印出来自`Child Class`的消息。这是因为子类中同名的方法覆盖了来自`Parent Class`的相同方法。

# Python 中的数据封装

数据封装是 Python 中 OOP 的一个方面，在这里你可以将类中的属性设置为私有。

```
import datetime**class** Rasyue:**def** __init__(self, post_name, post_author, post_date, blog_name):self.__post_name = post_nameself.__post_author = post_authorself.__post_date = post_dateself.__blog_name = blog_name**def** setpostname(self, new_post_name):self.__post_name = new_post_name**def** postdetails(self):date_time =  self.__post_date.strftime("%Y/%m/%d")print("The title of this post is "+self.__post_name+". It was written by "+self.__post_author+" on "+date_time+" on "+self.__blog_name)rasyue = Rasyue("Object-Oriented Programming in Python","Rasyue",datetime.date(2020, 7, 1),"Rasyue.com")rasyue.postdetails()rasyue.setpostname("Object Oriented Programming in PHP")rasyue.postdetails()
```

注意每个属性的双下划线，`__`。当您将属性设置为 private 时，您可以更改属性值的唯一方式是通过编写方法来更改值，我们通常称之为`**setter**`。

# Python 中的多态性

多态性是 OOP 中为多种形式(数据类型)使用公共接口的一个方面。让我们通过类方法来看看多态在 Python 编程语言中是如何工作的。

```
class Elephant:**def** __init__(self, name, weight):self.name = nameself.weight = weight**def** info(self):print(f"My name is {self.name}. I am {self.weight} kilogram and I am an elephant")**def** animal_sound(self):print("Rumbless~")**class** Tiger:**def** __init__(self, name, weight):self.name = nameself.weight = weight**def** info(self):print(f"My name is {self.name}. I am {self.weight} kilogram and I am a tiger.")**def** animal_sound(self):print("Roaaarr")elephant_test = Elephant("Dumbo", 125)tiger_test = Tiger("Jiba", 95)**for** animal **in** (elephant_test, tiger_test):animal.animal_sound()animal.info()animal.animal_sound()
```

为了解释以上内容，我们首先创建了两个类，`Elephant` & `Tiger`。这两个类具有相同的方法和属性。当我们试图在 for 循环中迭代它们时，即使两个对象不属于同一个类，也不会返回错误。然而，由于**多态性**，相同的方法名，我们可以忽略对象并概括调用相同的方法。

希望以上能够帮助你更好的理解 Python，或者对你有所帮助。我将会写更多关于 Python 的文章，敬请关注。