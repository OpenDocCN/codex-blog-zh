# 你这辈子不会太经常看到这个 Python 操作者了！

> 原文：<https://medium.com/codex/you-wont-see-this-python-operator-very-often-in-your-life-e954870243f1?source=collection_archive---------8----------------------->

![](img/7ceffaa9f3bf417e57ad4b54758e4736.png)

你好，你好吗？今天我要写的是 Python 的 ***最不为人知的*** (还有 ***有争议的*** )运算符之一，也是让吉多·范罗森从 **BDFL** ( *仁慈的终身独裁者*)的位置上下台的运算符。我将简要介绍它的工作原理和性能，然后我们将看几个使用场景。到本文结束时，您应该对该操作符做什么以及何时应用它有一个很好的概念。

让我们看一个简单的程序。

```
print(myval = 10)
```

当我试图打印上面的程序时，出现了一条错误消息，说“TypeError:'myval '是 print()的无效关键字参数。”它证明了我没有关键字参数“myval”。当然，如果我传递的参数是 print()函数的一部分，那么结果将与我期望的不同。

但是，你瞧，海象操作员*来拯救世界了。Walrus 是我们将在本文中看到的操作符。海象运营商的做法略有不同。*

## *海象操作员*

```
*print(myval := 10)*
```

*我将在赋值操作符前添加冒号“:”。然后我的屏幕上印了 10。这基本上是同时返还和分配。*

*海象操作员之所以这么叫，是因为它看起来有点像海象的眼睛和长牙。*

*![](img/546a6226195235cfc7a74c266818da1b.png)*

*海象— [来源](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fwww.gannett-cdn.com%2Fpresto%2F2019%2F09%2F24%2FUSAT%2F49d17e0e-3831-40c4-8230-fc5ccbafce94-GettyImages-508404787.jpg%3Fcrop%3D3804%2C2140%2Cx0%2Cy390%26width%3D3200%26height%3D1680%26fit%3Dbounds&f=1&nofb=1)*

## *要记住的事情*

*我想给你一些一般性的建议，告诉你当新特性*被引入一门语言、一个框架或者其他什么的时候，你应该有怎样的心态。避免这种非黑即白的想法，你实际上是在说我会一直用那个或者我永远不会用那个。想想它的特点，看看它的优点和缺点。**

**你现在必须意识到这个特性是在 Python 3.8 中引入的，所以如果你出于某种原因使用它，它将 ***与 Python 3.7 和更低版本的*** 不兼容，当然，很多程序员，尤其是对 Python 语言的每个小特性都不太感兴趣的初学者或中间用户，可能不理解你的代码或你到底在做什么，所以对一些人来说， ***可读性*** 在你使用 walrus 操作符的时候会是一个问题，因为它不是最受欢迎和使用最广泛的操作符，所以这一点要记住。**

**它不会增加任何你没有它就无法完成的事情，所以它不是你必须做的事情。它只是一个 ***新方法*** 做事，它可以帮你减少代码。**

## **实施例 1**

**让我们看看第一个例子，在这个例子中，walrus 操作符非常有用。在基于命令行的应用程序中，我们经常会遇到一些动态的用户输入，我们需要对其进行处理，例如在猜数字游戏、菜单或 hangman 游戏中，或者基本上在我们有一些反复执行的应用程序中，我们有不断关联的用户输入。**

**让我们看看下面这个没有 walrus operator 的程序:**

```
**done = Falsewhile not done:
    user_input = input("Enter something ('q' to quit): ")

    if user_input == 'q':
        done = True
    else:
        print(f"You entered {user_input}")**
```

****输出:****

**输入某物(' q '退出):Hi
你输入 Hi
输入某物(' q '退出):Hello
你输入 Hello
输入某物(' q '退出):q**

**现在的问题是我们有很多不必要的东西，我们可以用不同的方式去做。例如，如果我们可以直接检查 while 头中的终止条件，我们就不需要布尔值。没有 walrus 运算符时，修改后的代码如下所示:**

```
**while input("Enter something ('q' to quit): ") != 'q':
    user_input = input("Enter something: ")
    print(f"You entered {user_input}")**
```

****输出:****

**输入某物(' q '退出):Hi
输入某物:Hi
你输入 Hi
输入某物(' q '退出):hello
输入某物:hello
你输入 hello
输入某物(' q '退出):q**

**然而，这减少了代码行，但我仍然需要 ***处理 user_input*** ，所以我需要存储输入的任何内容，如果头中没有 walrus 操作符，我就无法做到这一点。**

**现在让我们使用 walrus 操作符尝试相同的逻辑:**

```
**while (user_input := input("Enter something ('q' to quit): "))!='q':
    print(f"You entered {user_input}")**
```

****输出:****

**输入某物(' q '退出):Hi
你输入 Hi
输入某物(' q '退出):hello
你输入 hello
输入某物(' q '退出):1 q**

**在这种情况下，我们只需要两行代码，少了一个 if 语句和一个变量。**

## **实施例 2**

**使用 walrus 运算符的另一个例子是在列表理解中。我们不仅可以使用它使它漂亮或花哨，而且还可以使用它提高执行速度，减少执行时间。假设我们有一个数字列表，我们有处理数字的函数。这只是一个简单的函数，但作为一个复杂的算法，它需要大量的时间来得出结果。所以，如果我传递一个数字，我需要 3 到 5 秒才能得到结果。**

```
**numbers = [5, 6, 13, 53, 191, 9, 35, 456, 201]def complicated_algorithm(num):
    return ((num * 12) / 2) + 3

my_result = [complicated_algorithm(x) for x in numbers complicated_algorithm(x) < 100]**
```

**我们可以看到这样的问题，我们调用了两次参数个数相同的函数，但不是每次都调用，因为我只在满足条件的情况下执行 complexized _ algorithm(x)(在 for 循环之前)，如果满足条件，我必须再次调用函数，因为我没有存储结果。因为这是一个复杂的算法，我们不想因为过于频繁地调用这个函数而浪费大量的时间。**

**所以我们能做的是，使用海象算子。我们可以保存 if 语句中 complexified _ algorithm(x)的结果，并使用该结果将结果添加到数组中，而不是调用该函数两次。**

```
**numbers = [5, 6, 13, 53, 191, 9, 35, 456, 201]def complicated_algorithm(num):
    return ((num * 12) / 2) + 3my_result = [result for x in numbers if (result := complicated_algorithm(x)) < 100]**
```

**如果 x 小于 100，Walrus 操作符将处理它，因为它已经计算了全部结果，只是为了查看它是否小于 100。为了避免浪费这个计算，我可以将结果保存在 result 变量中，然后将其作为结果插入到列表中。**

**在需要经常调用函数的复杂算法中使用这种 walrus 算子，可以减少执行时间，提高执行速度。我们来到了文章的结尾。很高兴坚持使用这篇文章。**

## **结论**

**在本文中，我们讨论了什么是 walrus 操作符，它的语法，以及如何使用它。在许多想要减少代码行数的场景中，walrus 操作符就派上了用场。值得注意的是，没有括号 的海象运算符 ***会导致语法问题。为了防止歧义和误解，在某些情况下避免使用 walrus 操作符。*****

# **最后做的事**

**希望你喜欢它并且学到了新的东西如果是这样，*一拍手👏而一跟着就会是* ***🤩可行走的🤩****有助于 Medium 推广这篇文章，让其他人也能阅读。*我是 Jagajith，下一集再来抓你。****

**灵感来自:[https://www.youtube.com/watch?v=Ydn83ajuqX8](https://www.youtube.com/watch?v=Ydn83ajuqX8)**