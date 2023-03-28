# Python:如何编写智能硬件包装器

> 原文：<https://medium.com/codex/python-how-to-write-smart-hardware-wrappers-833815bc8a16?source=collection_archive---------16----------------------->

## 以及为什么上下文管理器是你的朋友

在 [2020 StackOverflow 年度开发者调查](https://insights.stackoverflow.com/survey/2020#technology-most-loved-dreaded-and-wanted-languages-wanted)中，Python 是最受欢迎的编程语言。原因显而易见:Python 易于学习，易于阅读，并且它允许程序员用非常简单的语法来表达复杂的指令。Python 无处不在:web 开发、机器学习、数据科学，甚至在嵌入式系统上。(查看 [MicroPython](http://micropython.org/) ！)Python 也可以用来和硬件设备交互。随着 IO 引脚外露的单板计算机(如 Raspberry Pi)越来越受欢迎，低级开发对入门级程序员来说前所未有的容易。

硬件编程带来了一些有趣的挑战，习惯于高级开发的程序员可能并不熟悉。高级环境提供了许多好处，如垃圾收集、安全内存管理，甚至异步计算 API。这些特性非常棒，因为它们允许程序员专注于项目的细节，而不会在低级的怪癖上分心。不幸的是，与硬件交互并不总是那么容易。通常，您可能会通过操作系统级接口(如驱动程序或套接字)来处理硬件设备。这些结构被认为是“有状态的”:它们保留关于当前连接和先前事务的信息。这意味着有可能以坏的状态结束，在这种状态下，设备是快乐的或者没有响应。如果你曾经不得不拔掉再插上一个设备才能让它工作，或者反复开关某个东西，你知道我在说什么。这通常是软件出错的迹象，恢复的唯一途径是重新开始。

![](img/74fee5ac652f0f023600a7a56a74e694.png)

照片由[路易斯·里德](https://unsplash.com/@_louisreed?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

幸运的是，许多流行的硬件组件都带有 Python 库，您可以导入这些库，而不必担心底层交互。但如果你与硬件打交道的时间足够长，你将不可避免地发现一个没有附带库的传感器，并被迫自己实现“驱动程序代码”。这是设计挑战开始出现的时候。你如何为硬件提供一个方便的接口，同时又能处理本质细节？你能信任用户(使用你的库的程序员)以正确的顺序进行正确的调用吗？他们会关闭设备吗？发送不良数据？有许多考虑因素，在 Python 中，其中一些考虑因素比另一些更容易处理。

让我们特别考虑其中的一个问题:维护状态。我们如何编写一个包装器来保证我们的设备在每次使用时都能正确连接和断开？Python 语言为这个问题提供了一个很好的解决方案:上下文管理器。

上下文管理器只是一种帮助您管理资源的机制。资源可以是任何东西:文件、系统设备或句柄、套接字，甚至是分配的内存。资源可能很难管理，因为它们可能很难共享(在线程和进程之间)，并且对状态和范围很敏感(每次都需要打开和关闭)。上下文管理器旨在解决所有这些问题。要在 Python 中使用上下文管理的对象，您所需要的就是`with`关键字。例如:

```
with open('flavors.txt') as f:
    flavors = f.read()print('done!')
```

当解释器进入`with`语句时，它初始化文件资源。语句退出后(当我们离开缩进块时)，变量`f`超出范围，Python 释放资源并安全关闭。程序员不必担心其他人使用该文件，也不必担心如果没有显式调用`.close()`会导致的任何不良后果。相当甜蜜！

为了创建我们自己的支持上下文管理器的对象，Python 提供了两个内置的方法签名来实现:`__enter__()`和`__exit__()`。我们仍然会使用标准的`__init__()`(构造函数)方法。正如您可能推断的那样，当我们输入一个`with`语句时，`__enter__()`被调用，然后`__exit__()`被调用。Python 不会强迫任何人使用上下文管理器:不使用`with`仍然可以调用`open()`。但不管怎样，提供功能还是不错的。

让我们考虑一个例子:我们需要制作一个模块来与硬件组件交互，我们希望它是上下文管理的。硬件设备可以是我们打开串行连接的 USB 串行温度计:

```
class Sensor:
    def __init__(self):
        print("Opening device...")
        self._device = serial.Serial('COM3', 9600, timeout=1)def __enter__(self):
        print("Using context manager!")
        return selfdef __exit__(self, exc_type, exc_val, exc_tb):
        print("Automatically closing device!")
        self.close()def close(self):
        print("Closing device...")
        self._device.close()def measure(self):
        print("Measuring...")
        return self._device.read()
```

要使用传感器…

```
if __name__ == '__main__':
    with Sensor() as s:
        data = s.measure()
        print(f'Temperature: {data} degrees F')
```

输出是:

```
Opening device...
Using context manager!
Measuring...
Temperature: 42 degrees F
Automatically closing device!
Closing device...
```

enter 方法只是返回一个对对象的引用，因为无论我们是否使用上下文管理，都需要调用构造函数。exit 方法调用 close 方法(我们也公开该方法，以防使用该库的人不想使用上下文管理器)。exit 方法的附加参数提供了关于退出性质的额外细节，但是出于我们的目的，它们可以被忽略。

这是一个简单的例子，但是它展示了 Python 的上下文管理器有多么强大。即使有错误，我们的设备将总是被关闭，资源被释放，这意味着下次我们试图使用它时，它仍然会工作。

如需进一步阅读，请查看关于[上下文管理器](https://docs.python.org/3/library/contextlib.html)的 Python 文档。这个演示只是触及了表面:您可以探索如何使用装饰器、生成器，甚至异步上下文管理的资源。祝你好运！