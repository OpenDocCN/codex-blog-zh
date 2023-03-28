# 让你在 2023 年成为优秀 Python 开发者的 3 件事

> 原文：<https://medium.com/codex/3-things-to-make-you-a-superior-python-developer-in-2022-3a5ccd4a4ee1?source=collection_archive---------29----------------------->

毫无疑问，Python 是世界上使用最广泛的编程语言之一，经常与流行的高级语言如 C#、JavaScript 和 Java 争夺第一名。Python 的许多元素从一开始就保持不变，然而久而久之和 Python 的每个新版本都带来了更好的完成事情的方法和利用这些进步的新库。

![](img/3e2c85d8920f5574b5d1354d7e5419b8.png)

谢尔盖·塞明在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

你想和行业中的佼佼者竞争吗？这里有三个你应该利用的东西。

# 虚拟环境和包管理

Python 开发人员大量使用外部库来提供功能和修复错误。根据手头问题的复杂程度，将需要每种工具的不同版本。当你正在处理的另一个项目由于其依赖性而需要一个旧版本的框架时，使用任何工具来解决一个问题都是困难的。这是一个典型的问题，虚拟环境可以帮助解决。

对于简单的项目，你可以使用 Python 内置的[***【venv】***](https://docs.python.org/3/library/venv.html)*将项目和它们的需求放在一个单独的环境中。但是最近 Python 中增加的工具给了你更多的选择:*

*   *[**Pyenv**](https://github.com/pyenv/pyenv)**:**用 **pyenv** 管理各种 Python 版本轻而易举。即使您已经在 PC 上安装了 Python，pyenv 对于快速尝试新的语言特性或参与使用不同版本 Python 的项目也很有用。因为没有官方对 Windows 的支持，有基本支持的 [**pyenv-win**](https://github.com/pyenv-win/pyenv-win) 最近开始活跃起来。*
*   *[**Pipenv**](https://pipenv.pypa.io/en/latest/):Pipenv 设计用于管理虚拟环境以及您项目的所有依赖项。它还确保了依赖关系是确定性的——您得到了您想要的版本，并且它们在您指定的组合中工作。*
*   *[**Conda**](https://docs.conda.io/en/latest/) : Anaconda 是一个部署包管理库。它还包括大量用于机器学习、人工智能和大数据的科学 Python 库。Anaconda 包括一个名为 **conda** 的包和环境管理器，它在官方文档中被定义为——*任何语言的包、依赖和环境管理——Python、R、Ruby、Lua、Scala、Java、JavaScript、C/ C++、Fortran 等等。**
*   *[**诗**](https://python-poetry.org/) :当你在你的 Python 项目中使用外部包时，你必须确保你使用的是每个包的正确版本。软件包可能不再像更新前那样运行。依赖项管理器(如 Python poems)可以帮助您在项目中指定、安装和解析外部包。这样，您可以确保在每台机器上始终使用正确的依赖版本。*
*   *[**PDM**](https://pdm.fming.dev/latest/) : PDM 或 Python Development Master 意在成为下一代 Python 包管理工具。如果你对它们满意并且不想换另一个包管理器，坚持使用 **Pipenv** 或**poems**。然而，如果你在寻找那些工具中没有的东西，你很可能会在 **pdm** 中找到。*

# *新的 Python 特性*

*Python 的发展带来了该语言的大量额外增强。Python 在最近的版本(3.8 — 3.10)中加入了重要的语法结构，允许更强大和高效的编程。以下是一些最新增加的内容:*

*   *[**赋值表达式**](https://peps.python.org/pep-0572/) **:** 在 Python 3.8 中，新增了一个*海象运算符*“:=”。经营者的名字来源于这样一个事实，即从侧面看它像海象的眼睛和长牙。*

*![](img/48f50992af73ad9f12077c3e25b51aeb.png)*

*照片由[杰伊·鲁泽斯基](https://unsplash.com/es/@wolsenburg?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄*

*使用 walrus 运算符创建赋值表达式。我们可以使用运算符为 Python 表达式中的变量赋值。这是一个方便的操作符，帮助我们保持代码简短。*

```
*print(ready := True)*
```

*在一个步骤中，我们可以分配和打印一个变量。如果没有 walrus 运算符，我们会写:*

```
*ready = True
print(ready)*
```

*你需要利用它来避免各种重复，比如**重复函数调用**、**重复语句**和**重复迭代器调用**。*

*   *[**仅位置参数**](https://docs.python.org/3/whatsnew/3.8.html#positional-only-parameters):Python 3.8 版本中的许多新特性之一是能够通过使用/标记在函数声明中指定仅位置参数。对语法的这种改变提高了效率，并允许更好的 API 设计。*
*   *[**结构模式匹配**](https://peps.python.org/pep-0636/) :在 Python 3.10 中，包含了一个叫做‘结构模式匹配’的新特性。2020 年 6 月，PEP 622 提出了这一新功能的语法。Python 的模式匹配语句受到 Scala、Erlang 和其他具有类似语法的语言的启发。在其最基本的形式中，它的工作方式类似于 C、C++或 Java 中的 switch 语句。*

# *Python 测试*

*我们都同意，没有人，尤其是初学者，喜欢写测试。每个人都同意这是一件应该做的好事，但是只有少数人这样做。*

*用 Python 进行测试是一个很大的话题，有很多复杂性，但不一定很难。通过几个简单的步骤，您可以开始为您的应用程序创建简单的测试，然后从那里开始扩展。*

*虽然 Python 的内置测试框架 Unittest 默认情况下还不错，但它的设计和行为已经过时了。如果你想变得更加多才多艺，少写代码， [**PyTest**](https://docs.pytest.org/en/7.1.x/) 就是要走的路。*

# *结论*

*如果你想成为最好的，你应该不断地寻找新的趋势。维护者发布新版本是有原因的；无论是添加新特性让你成为更好的开发人员，还是修补程序错误，你只会从中受益。*

*感谢您的阅读！*

# *参考*

*[https://docs.python.org/3/whatsnew/3.8.html](https://docs.python.org/3/whatsnew/3.8.html)*

*[https://realpython.com/python-testing/](https://realpython.com/python-testing/)*