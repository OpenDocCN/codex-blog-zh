# Python 的上下文管理器用两个有启发性的例子来解释

> 原文：<https://medium.com/codex/pythons-context-managers-explained-with-two-instructive-examples-3cf8437224fb?source=collection_archive---------14----------------------->

## 关于魔术方法和@contextmanager 装饰器，您只需要知道。

你是否曾经在某人的 Python 代码中看到过类似于`with something(...) as s:`的一行，并且想知道这是关于什么的？或者您可能已经知道如何使用`with`语句，并且想要学习如何创建您自己的支持这种语法的对象类型？别担心，你来对地方了。在这篇文章中，我将解释 Python 上下文管理器有什么好处，如何…