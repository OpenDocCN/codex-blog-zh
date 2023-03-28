# Python 中列表的后台故事

> 原文：<https://medium.com/codex/how-a-python-list-operates-in-the-backend-954598305133?source=collection_archive---------3----------------------->

![](img/5bd9ffeeac47eb9248b57014a8498499.png)

Tobias A. Müller 在 [Unsplash](https://unsplash.com/collections/9361351/fps-deck?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

## 元素是如何存储的？参考？如何保持连续性？可变性？复杂？和其他各种信息。让我们在本文中弄清楚这一切&通过了解从我们声明一个列表的那一刻起在后端发生了什么，来巩固我们的 Python 列表基础。

I **引言**:你有没有遇到过一个表面上看起来很难理解或实施的话题？我相信我们都时不时地遇到过这样的话题，让我说清楚: *Python 列表*不在其中。你有没有想过当你声明一个列表时，后台会发生什么，元素是如何存储的，我们使用引用了吗？如何保持连续性，它是可变的吗，列表的复杂性。
在这篇文章中，我将分解 *Python 列表*并以简单明了的方式解决所有这些问题，使 *Python 列表* &的工作成为每个人都容易理解的话题。让我们深入一下，从学习 *Python 列表*的定义开始。

D 列表中的每个元素或值称为一个项目。就像字符串被定义为引号之间的字符一样，列表被定义为方括号之间的值。请小心，列表可以被认为类似于数组，但它们不是一回事。传统上，数组有固定的内存大小，而链表有动态的内存分配。在 Python 中工作时，将列表称为列表。当你在 PHP 或 Javascript 中工作时，称一个数组为数组。当你在 C 工作的时候，打电话求助！

```
**Following are some examples of lists in Python:**[ ] #Empty list
[1, 2, 3] #List of integers
[1, 2, 5.6, 9.8] #List of numbers (Floating point and Integers)
[‘a’, ‘b’, ‘c’] #List of characters
[‘a’, 1, 4.3, “Zero”] #List of mixed data types
[“One”, “Two”, “Three”] #List of strings.
```

因此，阅读上面的定义，我们可以推断出*列表是 python 中的一种数据结构*，有点类似于数组，但又不相似。在分解上述定义并用一个真实世界的例子来理解它之前，让我们先理解一下上述定义中使用的一些术语。

*   **元素**:我们存储在列表中的任何实体或对象，比如数值、字符串、布尔值。
*   数据结构:在计算机科学中，数据结构是一种数据组织、管理和存储格式，能够实现高效的访问和修改。
*   **Mutable:** 通俗地说，Mutable 是 Python 中使数据结构可编辑的属性，也就是说，我们可以在位置本身改变或更改数据结构的元素。我们将在本文稍后详细了解这一点。
*   **索引**:索引是存储列表组件的盒子的编号。索引从 0 开始，以 n-1 结束，它们用于访问和更改，n 是列表的大小，即列表中元素的数量。

让我们用一个真实的例子来解释列表，让我们更仔细地看看这个定义。我们将把火车与列表进行比较，所以火车的每个车厢可以被认为是一个存储元素的列表框，正如我们可以改变每个车厢中的乘客数量一样，我们也可以改变元素的值，我们还可以根据需要改变火车的大小，我们也可以对列表进行同样的操作，这使得它们是动态的，不像数组。也不同于数组，列表是异构的，允许我们存储许多数据类型的元素。下面你可以看到数组和列表之间的简单区别。

![](img/56382391c295b796eb138b86127cb3ad.png)

数组与列表

## 可变和不可变的概念

Python 数据类型可以大致分为两类——可变类型和
不可变类型。现在让我们详细讨论这两种类型。

*   因为 Python 中的一切都是对象，所以每个变量都保存一个对象
    实例。
*   当一个对象被初始化时，它被分配一个唯一的对象 id(内存中的地址)。
*   它的类型是在运行时定义的，一旦设置就不能改变。
*   但是，如果它是可变的，它的状态可以改变。换句话说，可变对象的值可以在创建后更改，而不可变对象的值不能更改。

注意:像(int，float，bool，str，tuple，Unicode)这样的内置类型的对象是不可变的。像(列表、集合、字典)这样的内置类型的对象是可变的。列表是可变的，因为我们可以在列表中插入/删除/重新赋值，而变量是不可变的。

## 元素是如何存储在列表中的？

每当我们在 python 中创建一个列表时，它都被存储在内存中，正如我们所讨论的，它是动态分配的，所以让我们详细看看元素是如何存储在列表中的。这个我们就简单理解了。
假设我们从创建一个包含元素的列表开始:

```
ls = [1, 2 , 'Manvendraaaa' , 1.2]**Length of list ls is 4 containg elements of integers , float , and string data types.** 
```

列表中的元素本身并不存储。相反，列表中的每个元素都存储在随机位置，并且列表存储这些随机位置的地址。

```
Suppose the elements of the list 'ls' are stored at random location with random address stated below in the format :variable_name = value --> address of random locationa = 1 --> 520
b = 2 --> 680
c = 'Manvendraaaa' --> 240
d = 1.2 --> 980The list will hold these addresses and it will look something like :
ls = [520,680,240,980]**Note : The addresses are only for demostration in real they are hexadecimal values.**
```

我希望上面的部分是清楚的，所以现在我们将继续讨论当我们改变列表中的一个元素时会发生什么。

```
So suppose we want to change the element 'Manvendraaaa' to 'Medium'. We know that the element is at index 2\. So as soon as we type the program to change it what happens in the backend. Let's see :ls[2] = 'Medium' #Command to change the element to 'Medium'**The moment we execute this command a new random location will be created with the element 'Medium' stored there and now instead of the variable 'c' pointing towards it will point towards the location where 'Medium' is stored as variable are immutable.
c = 'Medium' --> 460****Now c stores the address of the location where 'Medium' is stored & the list 'ls' stores the location of c as list are mutable. Keeping this in mind the array will look like :** ls = [520,680,460,980]
ls = [1, 2 , 'Medium' , 1.2]
```

所以从以上几点来看，很明显 *Python 列表在后端中使用引用*来存储列表中的元素。*引用*意味着使用地址来执行操作，而不是使用元素本身。 ***例如，变量‘a’保存的是存储 1 的位置的地址，列表‘ls’保存的是存储 a 的位置的地址。***

## 如何保持列表的连续性？

到目前为止，我们已经理解了可变性的概念，引用&元素是如何存储在列表中的。在学习后面的概念时，我们了解到列表使用引用存储元素，元素存储在随机位置，那么索引是如何工作的，我们如何维护列表的连续性呢？这就是调整大小的概念发挥作用的地方。

```
Whenever we created the list ls with 4 elements we reserved a continuous block of memory which can store 4 elements consecutively. The list we were having was : ls = [1, 2 , 'Medium' , 1.2]Suppose now we want to store a new element 'Happy' at the end of the list 'ls'. So we can't be sure that there is an empty space at the end of the list and for that reason python uses resizing for optimizing this problem. The moment we will use append to add the element 'Happy' at the end of list :ls.append('Happy')**So if there is no empty space at the end of the list 'ls' python will find a new block in the memory of double the size of the list 'ls' and copy all the element of the list 'ls' into it and now 'ls' will point to that new list & then append the new element at end at there is enough space for more new elements. This process is called Resizing of List and is used in python because lists are dynamically allocated. Finallyour list will look like :**ls = [1, 2 , 'Medium' , 1.2 , 'Happy'] #With more spaces left
```

## 为什么列表比数组慢？

从列表中读取要比从数组中读取多花 47%的时间。这是一个显著的差异，但不像写作那样显著，写作需要 695%的时间！那么为什么 List 慢了这么多呢？这有许多原因，但我将在本文中指出最明显的一个原因:

*   数组有固定的大小，因为它们是静态定义的&只能保存一种类型的数据，而存储在列表中的数据是异构的&因此，列表的大小不是静态的。
*   在写入列表时，我们需要检查可用空间和我们正在写入的数据类型，而在数组中，我们事先告诉数据类型，因此我们只存储一种数据，数组的每个块都有固定的大小，这与列表不同。
*   上述观点还表明，列表所需的大小通常大于数组所需的大小。

O 在你们跳到另一篇文章之前，这里有一篇文章的链接，关于“你应该知道的关于 Python 中的清单的 15 件事*”。*

*[](https://towardsdatascience.com/15-things-you-should-know-about-lists-in-python-c566792eca98) [## 关于 Python 中的列表，你应该知道的 15 件事

### Python 中列表的使用指南

towardsdatascience.com](https://towardsdatascience.com/15-things-you-should-know-about-lists-in-python-c566792eca98) 

本文详细解释了列表的不同方法/操作，并附有 Amanda Iglesias Moreno 的代码演示。一定要去看看，❤.* 

> *编者按:首先，也是最重要的，感谢你坚持写完这篇文章。如果你学到了新的东西，请留下掌声，并在未来关注更多这样令人惊叹的文章。也请给我你的反馈，告诉我如何提高我的写作水平。*