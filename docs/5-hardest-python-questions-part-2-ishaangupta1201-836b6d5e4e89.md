# 5 个最难的 Python 问题！第二部分

> 原文：<https://medium.com/codex/5-hardest-python-questions-part-2-ishaangupta1201-836b6d5e4e89?source=collection_archive---------0----------------------->

## 用这些问题迷惑你的 pythoneer 朋友

![](img/d7f3cfb745481dec82abeabaeef13276.png)

Andrea Piacquadio 摄于 Pexels

# 这个故事第一部分的链接—

[](/codex/5-hardest-python-questions-497a6df140d3) [## 5 个最难的 Python 问题！

### 用这些问题迷惑你的 pythoneer 朋友

medium.com](/codex/5-hardest-python-questions-497a6df140d3) 

# 1.)Python 里一切都是对象！🤨

猜测产量？

```
print(isinstance(object, type))
print(isinstance(type, object))
print(isinstance(type, type))
print(isinstance(object, object))
```

**回答:真，真，真，真**

这是因为在 python 中一切都是对象。所有类型如 *int、str 和 object* 都是一个*类型*类的实例，它是一个对象，因为 ***在 python 中一切都是对象。***

# 2.)sum()函数😨

猜猜这种情况下的输出？

```
print(sum(“”))
print(sum(“”, []))
print(sum(“”, {}))
```

**答案:0，[]，{}**

为了理解这一点，让我们看看 sum()是如何工作的-

***sum(iterable，/，start=0)***

从左到右对 start 和 iterable 的项求和，并返回总和。iterable 的项通常是数字，起始值不允许是字符串。因此，在上述所有情况下，“”被视为空序列，因此 *sum* 将简单地返回 *start* 参数作为总结果。

# 3.)Python 就是懒！😪

猜猜这种情况下的输出？

```
class follow:
   def func(self):
      return follow()a = follow()
follow = int
print(a.func())
```

**答案:0**

这是因为 python 函数内部的代码只有在调用时才会执行。这意味着所有的 *NameErrors* 都将被抛出，只有当我们真正调用这个方法时，变量才会被绑定。因此，在我们的例子中，在方法定义期间，Python 允许我们引用尚未定义的类。但是，在执行过程中 Python 会从外部范围绑定名字 ***跟随*** ，这意味着*函数*方法会返回一个新创建的 ***int*** 实例。

有点困惑吧？😅

# 4.)AttributeErrors？😏

猜猜这种情况下的输出？

```
print(sum([a.imag
   for a in [
      0, 5, 10e9, float(‘inf’), float(‘nan’)
   ]
]))
```

**答案:0.0**

您一定认为这会产生错误，对吗？不会的。这是因为 Python 中所有的数值类型像 ***int，float*** 等。；都继承自一个基本的*对象*类，它们都返回实部和虚部，其中也包括*和 ***NaN*** 。*

# *5.)数学又不行的地方！😵*

*猜猜这种情况下的输出？*

```
*a=(1 << 53)+1
print(a+1.0 > a)*
```

***答案:假***

*这个解释会很长，所以请耐心听我说:)*

*首先，由于**任意精度运算**(长运算)的反直觉行为。Python 支持 *long* 类型的非常大的整数，但是 Python 中浮点精度的限制是有限的。*

*号码是 2⁵ + 1 = 9007199254740993*

*Ssecond 是 **float 精度限制**，这意味着它不能完全表示为 Python float，因此，为了执行 *x + 1.0，* python 将 *a* 转换为 *float* ，将其舍入为 Python 可以轻松表示的 *9007199254740992.0* ，然后为其添加 *1.0* 。但是由于相同的表示限制，它将其设置回 *9007199254740992.0* 。*

*T hird 是由于**比较规则**。与其他语言不同的是，Python 不会因为 *float* vs *int* 比较而抛出错误，也不会试图将两个操作数转换为相同的类型。相反，它们比较实际的数值。并且由于 *9007199254740992.0* 低于 *9007199254740993，*打印**假**。*

**whewwww* ！😵‍💫*

# *最后的想法*

*那么这次你猜对了多少个输出呢？就在评论区！*

*如果您还没有阅读本文的第 1 部分，那么如果您现在不喜欢 python，请不要恨我😂。在 python 中还有很多类似的“陷阱”,但我认为这很棒，因为它将帮助您理解内部语言结构，并避免在您的项目中出现可能导致意外错误的用例。本文第一部分的链接已经在开始时分享了，去看看吧。与你的 Pythoneer 朋友分享这篇精彩的文章。😄*

**在我的下一篇文章中再见…**

*![](img/0579775bdb5273df16d7bcb360bf144d.png)*