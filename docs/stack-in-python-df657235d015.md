# Python 中的堆栈:如何、为什么和在哪里？

> 原文：<https://medium.com/codex/stack-in-python-df657235d015?source=collection_archive---------8----------------------->

![](img/a5e120869b6e3ae059b9b267a5e4316e.png)

信用:[https://unsplash.com/@eklektikum](https://unsplash.com/@eklektikum)

堆栈是一种线性数据结构，以后进先出的方式存储数据。Web 浏览器的后退按钮是一个堆栈应用程序。

我们在堆栈中执行两个主要操作，即 PUSH 和 POP。PUSH 操作将元素/值添加到堆栈中，而 POP 操作从堆栈中移除或删除元素/值。

# 堆栈中的一些基本操作

1.  **推送** —将一个元素或值添加到堆栈中。时间复杂度为 O(1)
2.  **弹出** —从堆栈中移除最顶端的元素或值。时间复杂度为 O(1)
3.  **TOP** —获取堆栈的顶部元素或值，而不移除它。它也被称为 PEEK。时间复杂度为 O(1)
4.  **清空** —检查堆栈是否为空。时间复杂度为 O(1)
5.  **满** —检查堆栈是否已满。时间复杂度为 O(1)
6.  **尺寸** —检查堆叠的尺寸。时间复杂度为 O(1)

# 堆栈的实现

在 Python 中实现堆栈有多种选择。在这里，我们讨论使用 Python 及其模块的堆栈实现。Python 中的堆栈可以通过以下方式实现:

*   目录
*   collections.deque
*   排队。LifoQueue

# 使用列表实现

Python list 可以作为栈使用。List **append()** 用于在堆栈中添加元素的方法，类似于堆栈推送操作。List **pop()** 用于从堆栈中移除元素的方法，类似于 stack **pop()** 操作。Python 列表有性能问题。列表**推送()**，**弹出()**操作变得越来越慢。如果列表增长并超出内存块，python 就会分配一些内存。这就是 Python 列表逐渐变慢的原因。时间复杂度为 O(n)。

让我们来理解下面的例子

```
# Define empty stack
stack = []# Add element to stack
stack.append(1)
stack.append(2)
stack.append(3)
stack.append(4)
stack.append(5)print("Display stack after PUSH operation")
print(stack)print("Remove element from stack")
print(stack.pop())
print(stack.pop())
print(stack.pop())
print(stack.pop())
print(stack.pop())print("Display stack after POP operation")
print(stack)
```

**输出:**

```
Display stack after PUSH operation
[1, 2, 3, 4, 5]
Remove element from stack
5
4
3
2
1
Display stack after POP operation
[]
```

在上面的代码中，我们定义了一个空列表。然后在堆栈中追加一些元素，类似于 stack **PUSH()** 操作。我们还使用 **pop()** 方法移除了元素。方法从列表中返回最后一个元素。

# 使用 collection.deque 实现

Python 的**集合**模块提供了**的**类。通过使用**德克**，我们可以创建一个堆栈。**队列**读作**卡牌**，意为**双端队列**。**dequee**比 Python **list** 好，因为它的追加和弹出操作比 Python **list** 快。时间复杂度为 O(1)。

让我们来理解下面的例子

```
# Define empty stack
from collections import dequestack = deque()# Add element to stack
stack.append(1)
stack.append(2)
stack.append(3)
stack.append(4)
stack.append(5)print("Display stack after PUSH operation")
print(stack)print("Remove element from stack")
print(stack.pop())
print(stack.pop())
print(stack.pop())
print(stack.pop())
print(stack.pop())print("Display stack after POP operation")
print(stack)
```

**输出:**

```
Display stack after PUSH operation
[1, 2, 3, 4, 5]
Remove element from stack
5
4
3
2
1
Display stack after POP operation
[]
```

# 使用 LifoQueue 实现

Python **队列**模块有 **LifoQue** ，和栈一样。它有 **put()** 方法将元素添加到堆栈中，有 **put()** 方法将元素从堆栈中移除。

# LifoQueue 中的一些基本操作

1.  **put()** —将元素添加到堆栈中。如果队列已满，它会一直等到有空间可用。
2.  **put_nowait()** —将元素添加到堆栈中。仅当空闲槽立即可用时，才将项目排入队列。否则，引发完整异常。
3.  **qsize()** —返回队列的大概大小(不可靠！)
4.  **empty()** —如果队列为空，则返回 True，否则返回 False(不可靠！)
5.  **maxsize** —用于设置队列的最大大小。如果 maxsize 是< = 0，那么队列大小是无限的。
6.  **full()** —如果队列已满，则返回 True，否则返回 False(不可靠！)
7.  **get()** —从队列中移除并返回一个项目。
8.  **get_nowait()** —从队列中移除并返回一个项目，而不阻塞。只有当一个项目立即可用时，才获取该项目。否则引发空异常。

让我们来理解下面的例子

```
# Define empty stack
from queue import LifoQueuestack = LifoQueue(maxsize=5)print(f"Size of stack: {stack.qsize()}")
# Add element to stack
stack.put(1)
stack.put(2)
stack.put(3)
stack.put(4)
stack.put(5)print(f"Stack is Full: {stack.full()}")
print(f"Size of Stack: {stack.qsize()}")print("Remove element from stack")
print(stack.get())
print(stack.get())
print(stack.get())
print(stack.get())
print(stack.get())print(f"Stack is Empty: {stack.empty()}")
```

**输出:**

```
Size of stack: 0
Stack is Full: True
Size of Stack: 5
Remove element from stack
5
4
3
2
1
Stack is Empty: True
```

最初，堆栈大小为 0。然后，我们使用 **put()** 方法将 5 个元素推入堆栈。然后，我们检查堆栈是否已满。然后，我们检查堆栈大小。之后，我们使用 **get()** 方法从堆栈中移除元素。最后，我们检查堆栈是否为空。

# Python 堆栈和线程

我们可以在多线程程序中使用 Python 堆栈。在多线程编程中使用**列表**可能很危险，因为它不是线程安全的。

**deque** 有点复杂，因为它的 **append()** 和 **pop()** 方法是原子的，这意味着它们不会被其他线程中断。

所以，用 treading 构建一个 Python 栈的程序， **LifoQueue** 比较好。它使用 **put()** 和 **get()** 来添加和移除堆栈元素。

# 堆栈的应用

虽然 stack 是一种实现起来很简单的数据结构，但是它非常强大。堆栈最常见的用途是:

1.  **把一个单词倒过来**——把所有的字母堆成一堆，弹出来。由于堆栈的后进先出顺序，您将得到逆序的字母。
2.  **在编译器中** —编译器通过将表达式转换为前缀或后缀形式，使用堆栈来计算类似`2 + 4 / 5 * (7 - 9)`的表达式的值。
3.  **在浏览器中** —浏览器中的后退按钮将您之前访问过的所有 URL 保存在一个堆栈中。每次你访问一个新的页面，它都会被添加到堆栈的顶部。当您按下“后退”按钮时，当前 URL 将从堆栈中删除，并访问以前的 URL。
4.  **函数调用和返回过程** —当我们从另一个函数调用一个函数时，那个函数调用语句可能不是第一个语句。在调用函数之后，我们也必须从函数区回到我们离开控制的地方。所以我们想继续我们的任务，而不是重新开始。为此，我们将程序计数器的地址存储到堆栈中，然后转到函数体来执行它。执行完成后，它从堆栈中弹出地址，并将其分配给程序计数器，以再次恢复任务。
5.  **回溯过程** —回溯是算法设计技术之一。为了这个目的，我们深入某种方式，如果这种方式没有效率，我们就回到先前的状态，进入其他路径。为了从当前状态返回，我们需要存储以前的状态。为此，我们需要一个堆栈。回溯的一些例子是寻找骑士巡游问题或 N 皇后问题的解决方案等。
6.  **内存管理** —内存的分配发生在连续的内存块中。我们称之为堆栈内存分配，因为分配发生在函数调用堆栈中。编译器知道要分配的内存大小。当一个函数被调用时，它的变量获得堆栈上分配的内存。当函数调用完成时，变量的内存被释放。所有这些都是在编译器中一些预定义例程的帮助下发生的。用户不必担心内存分配和堆栈变量的释放。
7.  表达式转换——将一种形式的表达式转换成另一种形式是栈的重要应用之一。

*   前缀中缀
*   后缀中缀
*   中缀前缀
*   后缀的前缀
*   后缀到中缀
*   后缀到中缀

8.括号匹配—给定一个表达式，您必须找出括号是否正确匹配。例如，考虑表达式`( a + b ) * ( c + d`。在上面的表达式中，括号的开始和结束被正确地给出，因此它被认为是正确匹配的括号表达式。然而，表达式`(a + b * [c + d )`不是有效的表达式，因为括号给得不正确。

9.用于许多算法，如汉诺塔，树遍历，股票跨度问题，直方图问题。

10.在像拓扑排序和强连通分量这样的图形算法中

# 结论

我们已经提到了用 Python 实现堆栈的三种方法。以上方法各有利弊。

我们使用堆栈和线程，你应该使用 LifoQueue，但是要确保它弹出和推送元素的性能。但是如果你没有使用线程，使用一个队列。

我们也可以使用这个列表来实现堆栈，但是这个列表可能会有潜在的内存重新分配问题。list 和 dequee 在接口中是相同的，但是 dequee 没有内存分配问题。