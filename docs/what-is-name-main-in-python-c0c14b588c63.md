# Python 中 __name__ == "__main__ "是什么？

> 原文：<https://medium.com/codex/what-is-name-main-in-python-c0c14b588c63?source=collection_archive---------0----------------------->

## Python 中 main 方法什么时候执行，怎么执行，什么意思？

![](img/d7a7f32705a73daa8fe8c7305604bfcc.png)

[图片](https://www.freepik.com/premium-photo/young-brunette-woman-looking-sideways-with-doubtful-skeptical-expression_5362123.htm#query=thinking&position=11&from_view=search)由 [asier_relampagoestudio](https://www.freepik.com/asier-relampagoestudio) 在 [freepik](https://www.freepik.com) 上拍摄

如果您是 Python 的新手，您可能已经注意到，可以使用或不使用 main 方法来运行 Python 脚本。但是现在你会想为什么我们需要它。

在这个故事中，我将解释 main 方法的用途，以及当您定义它时会发生什么。

# if __name__ == "__main__ ":是做什么的？

在执行代码之前，Python 解释器读取源文件并定义一些特殊变量/全局变量。如果 python 解释器将该模块(源文件)作为主程序运行，它会将特殊的`__name__`变量设置为值`**“__main__”**`。如果该文件是从另一个模块导入的， `__name__` 将被设置为**模块的名称。**模块名可作为`__name__`全局变量的值。

模块是包含 Python 定义和语句的文件。文件名是带后缀的模块名。py 已附加。

当我们执行一个文件作为 python 解释器的命令时，

```
python follow.pyprint ("Executed")if __name__ == "__main__":
 print ("Executed when invoked directly")
else:
 print ("Executed when imported")
```

*   缩进级别为 0 [Block 1]的所有代码都会被执行。定义的函数和类是定义好了的，但是它们的代码都没有运行。
*   在这里，如同我们直接执行 script.py 一样`__name__`变量将会是`**__main__**`。因此，只有当该模块是程序的入口点时，if 块[Block 2]中的代码才会运行。
*   因此，您可以通过测试`__name__`变量来测试您的脚本是直接运行还是由其他东西导入。
*   如果脚本被其他模块导入，那么`**__name__**` 将成为模块名。

# 我们为什么需要它？

例如，我们正在开发一个旨在用作模块的脚本:

```
# Python program to execute function directlydef my_function():
 print ("I am inside function")# We can test function by calling it.
my_function()
```

现在，如果我们想通过导入来使用那个模块，我们必须注释掉我们的调用。最好的方法是使用下面的代码:

```
# Python program to use main for function call.if __name__ == "__main__":
 my_function()import myscriptmyscript.my_function()
```

# **优点:**

1.  每个 Python 模块都定义了它的`__name__`,如果这个是`‘__main__’`,这意味着用户正在独立运行该模块，我们可以做相应的适当动作。
2.  如果您将该脚本作为一个模块导入到另一个脚本中，那么`__name__`将被设置为该脚本/模块的名称。
3.  Python 文件既可以作为可重用模块，也可以作为独立程序。
4.  `if __name__ == “main”:`用于执行一些代码**只有**文件是直接运行的，而不是导入的。

# 最后的想法

好吧，希望现在你知道了`__name__ == “__main__”`在 Python 中的用法。我希望这篇文章对你有所帮助，并让你学到一些新东西。把这篇文章分享给你的 Pythoneer 朋友吧。

## **快乐编码！**