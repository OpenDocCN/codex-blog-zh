# 用 Python 构建数独解算器和生成器(3/3)

> 原文：<https://medium.com/codex/building-a-sudoku-solver-and-generator-in-python-3-3-cac73d340973?source=collection_archive---------7----------------------->

![](img/083d426c32cd3c9bc7565073ff92e1a5.png)

# 完成发电机

当我们玩数独游戏时，98%的时候我们期望只有一个解，因此我们需要生成只有一个解的数独游戏，这样我们就可以标记它是对还是错。为此，我们需要两个辅助函数和一个主函数，在生成电路板时调用。总的想法是，我们找到棋盘上不同的空位置，在每个位置上求解数独棋盘，并查看总共有多少个解。

首先，我们将创建两个助手方法，类似于我们为求解器所做的事情，这将允许我们获得一个给定数独棋盘的所有可能解的列表

这是一个求解方法，用于找出给定数独棋盘的解的数量，它接受两个与起始行和起始列相关的参数，然后使用我们传统的求解方法求解数独。这就是为什么一个数独游戏可以从棋盘上的多个空格中解决。

下一个方法是找到它遇到的第一个空白空间，但是当然有一个转折(否则我们为什么要做另一个方法？):

这个方法接受一个 board 作为参数，这可能感觉怪怪的，因为到目前为止我们一直使用 board 属性，以及一个整数。在该方法中，每找到一个空单元格，变量就会递增，但是，如果该变量等于作为第二个参数传入的整数，则当前空单元格的行和列将在元组中返回。

我们要编写的下一个方法是 main 方法，它返回特定数独棋盘的解的数量:

这是主要的公共方法，可以调用它来查找当前棋盘的解的数量。首先将变量 _z 设置为棋盘上空白单元格的数量，并创建一个 for 循环来多次迭代 _z。在每次迭代期间，创建当前对象的一个[深拷贝](https://www.educba.com/python-deepcopy/)。这样我们就可以在不影响主对象的情况下编辑棋盘对象的副本。使用这个板副本，调用两个助手函数，板的解决方案被附加到解决方案列表中。最后，创建解决方案列表的集合列表，这是为了从列表中移除任何重复的解决方案。

# 结束这一切

为了结束一切，我们需要创建最后一个方法，它将生成一个单元格被删除的数独板，以及该板的解决方案:

这个公共方法接受一个完全填充的板和一个困难作为参数。制作另一个深拷贝，这一次是填充板。有 3 个难度级别，每个级别描述了 81 个方格中有多少被从棋盘上移除。移除的方块越多，难度越大，生成棋盘的时间也越长。

首先，在 3 个 while 循环中，12 个方块从整个棋盘上消失。然后，当计数器的当前值小于要移除的方块数时，程序将继续移除随机的方块。只有当棋盘有一个解时，计数器才会增加，否则被选择擦除的方格将返回到其原始值。一旦我们达到了数独只有一个解的状态，并且移除了足够多的单元格，问题板和解板(完全填充的板)都被返回。

当然，我们可以将这个函数打包到另一个方法中，该方法只接受难度作为参数，并调用这个主函数，以及生成随机完整棋盘的方法。然后这两者都以代码格式返回:

# 最后的想法

这就结束了我的关于如何制作一个求解和生成数独引擎的 3 部分教程，这里有一个关于如何在你自己的脚本中使用它的简单例子:

如果你喜欢这个教程，有任何问题或建议，那么请随时在这里留下评论，我一定会回答他们。

感谢您的阅读！💖