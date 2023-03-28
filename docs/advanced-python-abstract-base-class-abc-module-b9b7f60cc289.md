# 高级 Python:抽象基类和 abc 模块

> 原文：<https://medium.com/codex/advanced-python-abstract-base-class-abc-module-b9b7f60cc289?source=collection_archive---------6----------------------->

Python 提供了一个包抽象基类(ABC)来帮助实现抽象类，这个模块被称为`abc`是有原因的。抽象类允许开发人员为子类产生蓝图，使其具有特定的行为，所以让我们从一个常见的数据工程场景的用例来看看我们是如何做到这一点的…

![](img/49fadf49d70e1524a09e357a83131b17.png)

Joshua Aragon 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 背景上下文

假设我们正在编写代码，为您的机器学习摄取系统开发一个数据格式转换器，转换成您选择的客户格式。因为数据系统是活的有机体，它们的内部化学一直在变化，而且有大量不同的数据格式可用，所以最好用面向对象的方法来解决这个问题。因此，让我们为转换器创建一个类。

如前所述，我们面临的两个主要设计挑战是多种数据格式和输入数据的可能变化。因此，为了解决这个问题，我们需要一个基类，它提供了一个蓝图，必须在我们不同的转换器子类中具有功能。

现在让我们创建一个抽象类来定义我们的基本转换器的蓝图。我的自定义格式的名称将是`Naruto`。

```
class NarutoConverter():
    def convert(self): pass
    def clean_string(self): passclass JSONtoNaruto(NarutoConverter):
    def __init__(self, input_file):
        self.__input = input_file
```

我们的转换器有两种方法`convert`将输入格式转换成`Naruto`格式，还有`clean_string`方法处理输入字符串的不同字符串质量。

现在，由于我们正在创建抽象类，我们不希望任何人创建这个类的实例，因为它只是转换器的蓝图。然而，我们创建上面这个类的方式并不妨碍这个类的实例化。

上述代码的第二个问题是，它没有强制子类实现`convert`和`clean_string`方法的功能。在这种情况下，即使方法没有实现，上面的`JSONtoNaruto`代码也不会抛出错误。让我们在下一节看看如何处理这些问题。

# Python 的 abc 模块

现在让我们看看如何用`abc`库声明`NarutoConverter`并实现我们的抽象目标。

```
from abc import ABC, abstractmethodclass NarutoConverter(ABC): @abstractmethod
    def convert(self): pass @abstractmethod
    def clean_string(self): pass
```

基本方法包括从`abc`模块创建一个抽象类作为`ABC`的子类，并在每个抽象方法声明之前使用装饰器`@abstractmethod`。这有助于将抽象类功能增强到您的自定义子类中。现在，如果我们试图实例化`NarutoConverter`类，它将引发一个异常并阻止这个抽象类的实例化。现在让我们看看子类`JSONtoNaruto`的实现。

```
class JSONtoNaruto(NarutoConverter):def __init__(self, input_file):
        self.__input = input_file

    def convert(self):
        naruto_file = self.__input
        return naruto_file def clean_string(self):
        clean_str = self.__input.str
        return clean_str
```

在上面的代码中，我们实现了这两种方法。如果我们在没有实现任何一个方法的情况下运行代码，那么`python`会引发以下错误。这种行为是通过使用`@abstractmethod` decorator 来实现的，它可以防止没有方法实现的类实例化。

```
TypeError: Can't instantiate abstract class JSONtoNaruto with abstract methods convertTypeError: Can't instantiate abstract class JSONtoNaruto with abstract methods clean_string
```

# 结论

数据工程处理管道，由于管道 DAG 中有许多移动部分，面向对象是设计管道代码时非常常见的方法。设计类的蓝图是数据工程师经常处理的事情(除非你是一个函数式编程范型的人),以产生可重用的管道组件。抽象类提供了一种简单的方法来为各种管道组件提供设计蓝图，并在扩展时保持代码库的整洁、逻辑和可管理性。

## 请务必关注并订阅，以便尽早获取内容。

***关于我:我是一名数据工程师，在 GCP、SQL 和 Python 堆栈方面经验丰富，帮助初创公司设置和扩展他们的数据基础设施。想合作吗？在***[***Linkedin***](https://www.linkedin.com/in/yuvrender-gill/)***上联系我。***