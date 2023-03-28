# 我们在使用熊猫时都会犯这种常见的错误。

> 原文：<https://medium.com/codex/we-all-make-this-common-mistake-while-using-pandas-622fce2938e1?source=collection_archive---------24----------------------->

## 相信我，它们很容易修理…

![](img/4406d14ef7faa89d57104fca09145692.png)

[来自](https://www.pexels.com/photo/1-1-3-text-on-black-chalkboard-374918/)

今天，在这个世界上，我们都会犯错误，即使在数据世界里，很多数据科学家在涉及熊猫时也会犯错误。虽然这些错误很容易避免，但是修复它们可以使您的代码更具可读性和可理解性。

# 错误 1:让熊猫寻找数据类型

当您将数据导入到数据帧中，并且不告诉 Pandas 您的列和数据类型时，Pandas 会将整个数据集读入内存，以确定数据类型。

让我们假设，如果您有一个充满文本的列，熊猫将读取每个值，并查看它们是否都是字符串，在确认后，它会将该列的数据类型设置为“字符串”。然后对所有其他列重复这个过程。

通常，我们使用`df.info()`来查看一个数据帧使用了多少内存。这与熊猫计算每一列的数据类型所花费的时间大致相同。

为了解决这个问题，只需添加`dtypes`参数和一个字典，将列名及其数据类型作为字符串。例如:

```
pd.read_csv(‘Student_profiles.csv’, dtype={
    ‘Name’: ‘str’,
    ‘Class’: ‘str’,
    ‘Grade’: ‘str’
})
```

# 错误 2:剩余的数据帧

熊猫数据框的最大优点是它们很容易创建和修改。但不幸的是，这样做的副作用是大多数人最终会得到这样的代码:

```
# Change dataframe 1 and save it into a new dataframed
f1 = pd.read_csv(‘file.csv’)df2 = df1.dropna()df3 = df2.groupby(‘thing’)
```

尽管你已经搬到`df3`并开始使用它。不要在内存中留下多余的数据帧。如果你用的是笔记本电脑，它会影响你几乎所有其他工作的表现。如果你在一个服务器上，这将会影响服务器上与你一起工作的其他人的性能。

相反，保持你的记忆干净:

*   在一行中链接多个数据帧修改:

`df = df.apply(thing1).dropna()`

*   *正如* [*罗伯托·布鲁诺·马丁*](/@bobbruno_6876) *一样，另一种清理内存的方法是* ***执行函数*** *内的操作。您仍然可能无意中以这种方式滥用内存，解释范围超出了本文的范围，但是如果您不熟悉，我鼓励您阅读本文。*

[https://www . data camp . com/community/tutorials/scope-of-variables-python](https://www.datacamp.com/community/tutorials/scope-of-variables-python)

# 错误 3:手动配置 Matplotlib

这可能是最常见的错误，因为它影响最小。

Matplotlib 是熊猫自动发货的，它还会在每个数据帧上为我们生成一些图表配置。因此，当它已经为您填充到 Pandas 中时，没有必要为每个图表导入和配置它。

这里有一个错误做法的例子，尽管这是一个基本图表，但仍然是浪费代码:

```
import matplotlib.pyplot as plt
plot1.hist(x=df[‘x’])
plot1.set_xlabel(‘label for column X’)
plt.show()
```

**正确的做法:**

`df[‘x’].plot()`

更容易，不是吗？你可以在这些[数据帧绘图对象](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.plot.html)上做任何你可以对任何其他 [Matplotlib 绘图对象](https://matplotlib.org/3.1.3/api/_as_gen/matplotlib.pyplot.plot.html)做的事情。例如:

`df[‘x’].plot.hist(title=’Chart title’)`

# 结论

我确信并且坦率地说，我犯了这些错误，而且我还犯了一些我不知道的愚蠢的错误。但是希望与您和这个数据世界社区中的其他人分享这些已知的方法将有助于更好地使用您的硬件，**让我们编写更少的代码，完成更多的工作！**

别忘了留下你的回答。✌

大家敬请关注！！为了把我的故事发到你的邮箱里，请订阅我的时事通讯。

感谢您的阅读！不要忘记给你的掌声，分享你的回答，并与朋友分享！