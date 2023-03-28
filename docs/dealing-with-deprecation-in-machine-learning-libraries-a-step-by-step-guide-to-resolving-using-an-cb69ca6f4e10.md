# 处理机器学习库中的弃用——使用示例解决问题的分步指南

> 原文：<https://medium.com/codex/dealing-with-deprecation-in-machine-learning-libraries-a-step-by-step-guide-to-resolving-using-an-cb69ca6f4e10?source=collection_archive---------9----------------------->

![](img/786c70e3a2b2cc27ed154891cb688c11.png)

所有的软件功能，不管是哪种语言，都会随着时间的推移而发展，结果是曾经使用过的功能会变得过时并停止工作。在我的情况下，仅仅几周或几个月前，一个全功能的机器学习/深度学习算法现在位于一个无法运行的破碎的 python 脚本中。在编程界，这被称为弃用，Python 机器学习库也不例外。那么我们该如何应对呢？

在这里，我将演示与机器学习教程相关的弃用问题，以及我如何着手解决它。本文的目的是教育您，如果您不熟悉 API 库文档，那么如果您遭遇同样的命运，您也可以轻松地修复您的程序。

如果你读过我最初的媒体文章，题为“人工智能入门”([https://miteshparmar 1 . Medium . com/Getting-Started-with-AI-ml-nn-a-beginners-guide-b 1659d 6 bffd 7](https://miteshparmar1.medium.com/getting-started-with-ai-ml-nn-a-beginners-guide-b1659d6bffd7))，我说的是一个名为 sentdex 的 YouTuber，它有学习机器学习和深度学习的后续教程。他的一个教程名为‘Part 35——为机器学习处理非数值数据([https://python programming . net/working-with-Non-Numerical-Data-Machine-Learning-tutorial/？completed =/machine-learning-clustering-introduction-machine-learning-tutorial/](https://pythonprogramming.net/working-with-non-numerical-data-machine-learning-tutorial/?completed=/machine-learning-clustering-introduction-machine-learning-tutorial/))写于 2016 年 6 月，不幸的是现在(2021 年 3 月)有两个不推荐使用的函数，因此不折不扣地遵循本教程，逐字复制和执行这段代码是行不通的。

让我们来看看这个教程，并通过如何修复它的步骤…

我在 Python 脚本的最顶端插入了注释，放在一个“文本单元格”中，里面有网站和 YouTube 视频的链接。然后，我的导入语句被插入到底部所示的第一个“代码单元”中:

![](img/c3cb2541e6041e52ef403b547bac9073.png)

我已经安装了我的 Google Drive(如左栏所示),并相应地引用了数据集。第二行将变量“df”赋给数据集(这是一个熊猫 Dataframe 对象。注意:您可以使用 print(type(df))函数来确定 df 是什么类型的对象。单元格代码的最后一行是检查使用 pd.read_excel(path)加载的数据。执行此代码单元格将数据显示在一个漂亮整洁的表格中，如下所示:

![](img/31c5fdbc933dd0e89fb1889a7e0d2063.png)

紧接在它下面，我插入了一个“文本单元格”来说明下一步需要做什么(如 sentdex 在本教程中所述)。然后，我插入了另一个“代码单元”，并输入了代码，该代码省略了加载的表中的两列(df.drop(columns=['body '，' name'])。执行此代码单元时的行错误:df.convert_objects 如下所示:

![](img/de3c371b088fd389a588a6792c1788d4.png)![](img/a5ca660b6ab20b4ea770daa9e4e78da5.png)

我们的错误是立即告诉我们“DataFrame”对象没有属性“convert_objects ”,并用弯弯曲曲的红线突出显示错误代码(代码单元格的第 5 行)。

当一个开发人员读到这里时，它立即意味着 Pandas DataFrame 库的 convert_objects 函数不存在。

问:那么我们下一步做什么？

A.使用谷歌做一些调查…

跳到谷歌上，输入我们的图书馆名称；熊猫，物体名称；数据帧和函数名；convert_objects 并按 Enter 键:

![](img/7fdeb0f59422b8d04aea63ba2265e8c6.png)

谷歌的第一个结果是熊猫官方文档，也称为 API(应用程序编程接口)。它是由库的开发者定义的关于如何使用函数和构造代码的详细描述的文档，所以让我们来看看这个。点击链接…

![](img/df6006a6c004cc0946e56c0ceba2a0c4.png)

您瞧，文档中写着“从版本 0.21.0 起已弃用”:

![](img/770cb6e390f4c9788f9a8da077b4a332.png)

这就是我们问题的症结所在！基本上，我们使用的是高于 0.21.0 的版本，该版本不支持 convert_objects 函数。请注意，就在“已弃用”红色框的上方，突出显示了“*尝试为对象列*推断更好的 dtype”。这是 convert_objects 函数的功能描述(dtype 表示数据类型)。

不幸的是，这个 API 文档也没有告诉我们使用什么其他替代函数(可能是因为它没有被维护并且已经被取代)。

所以回到谷歌，搜索“熊猫数据框架转换数据类型”并按下回车键:

![](img/6ead356a781ce1bbda98179605569369.png)

我们的第一个结果是熊猫官方的最新文件:

![](img/32ee39b73b312b4e5c5b0471b4a3852d.png)

**边注:**

版本 1.2.3 是熊猫的最新版本，如果你用谷歌搜索“熊猫的最新版本是什么”,它会告诉你:

![](img/8f7d4d04da011b8dc694c5378893c520.png)

在 Colab 中，在“import numpy as np”下面插入“print(pd。_ _ version _ _)’并执行单元代码:

![](img/af6471a577bcfb56c5e965c77bdaf34e.png)

好的，所以 Colab 运行的不是 Pandas 的最新版本，而是版本 1.1.5，这个版本比 sentdex 的带有不推荐使用的函数的教程更新。不用担心，不用在 Colab 更新熊猫。这个版本仍然有效。

**回到主文:**

https://pandas.pydata.org/是最新熊猫 API 文档的官方网站。所以点击谷歌显示的第一个链接。

现在我们需要一个以“convert”开头的函数，让我们将它输入到搜索栏中，然后按回车键。结果列表中显示的第一个函数是熊猫。DataFrame.convert_dtypes。这看起来可能会让你感兴趣，所以请点击链接…

![](img/8972e1931273913f88316385a14659be.png)

最新的 Pandas API 文档用黄色突出显示了我们正在搜索的单词，在我们的例子中是“convert”。用红色突出显示的描述是“使用支持 pd 的 dtype 将列转换为可能的最佳 dtype”。NA”:

![](img/b3f98537679c5cd43176324e9b798534.png)

看起来我们在这个函数上赢了！向下滚动到注释部分，查看更详细的描述:

![](img/9a0a70d1b8f6d3ac320f143d84af9749.png)

第一段准确地告诉我们这个函数将做什么；尝试将一个系列或数据帧中的一个系列转换为支持 pd.NA 的数据类型。第二个突出显示的部分告诉我们，该函数将尝试将任何整数数据类型转换为适当的整数扩展类型(我不想教您“吸鸡蛋”，但我想我会提到这一点，以便您阅读它而不是浏览它)。

向下滚动显示了一个很好的例子，从创建各种数据类型的 Pandas dataframe 对象开始，显示数据类型，创建变量；convert_dtypes()函数的 dfn，然后在运行该函数后显示数据类型:

![](img/07b3363121c45f6cfa10c9c048772bd0.png)

如图所示，NaN 值被替换为<na>，并且数据类型的比较表明，原来的“对象”数据类型现在被转换为“字符串”或“布尔”。int32 变成了 Int32，在 e 列中，float64 数据类型变成了 Int64。</na>

滚动到页面的最后部分向我们展示了另一个例子:

![](img/f5ec735b2df351d548c0f32bf10f6a4f.png)

从根本上说，它告诉我们，如果 NaN 存在，convert_dtypes()函数会将其更改为<na>，这可以在我们的 Python 脚本中处理。</na>

这个 convert_dtypes()函数就是“蜜蜂膝盖”！这正是我们所需要的，以取代不支持的 convert_object()函数。

让我们把它放到 Python 脚本中，注释掉这个不推荐使用的函数，并在它上面写一个注释。执行到这个“代码单元”的所有内容，现在显示另一个错误，这次指向下一行；df.fillna(inplace=True)如图所示:

![](img/8c89253ff1d3fa42d9ca5b577fc89a51.png)

错误状态:值错误:必须指定填充“值”或“方法”

所以回到最新的熊猫 API 文档中搜索 fillna:

![](img/c0ae934c134173b1f508c8a21669e32f.png)

从返回的列表中，感兴趣的是声明 DataFrame.fillna 的列表，因为这是我们正在使用的函数。请点击此链接:

![](img/6e6c78c4b410478a34ece44039656c3a.png)

不幸的是，这个文档并没有告诉我们哪些参数是强制的，哪些是可选的(这是代表熊猫开发者的糟糕的 API 文档)。用红色突出显示的是我们感兴趣的东西；“值”和“方法”是前两个参数。

现在，从我们在 Colab 中报告的错误来看，fillna 参数不能为空(当 sentdex 制作本教程 Pandas 时，它确实允许，但后续版本不允许)。这意味着我们必须指定一个参数。向下滚动查看示例:

![](img/5310fd596ae73c5f7502f2a297545bcf.png)

同样，我们看到了各种数据类型的 DataFrame 对象的创建，并包含 NaN。因为我们不想用 0 替换 NaN，所以 df.fillna(0)不是一个选项。但是第二个突出显示的框显示了当我们使用 df.fillna(method='ffill ')时，通过向前或向后传播 NaN 值来处理非空值。

我不知道你怎么想，但是“向后或向前传播非空值”这个描述太模糊了。它没有给我一个关于这个参数实际作用的精确描述(我真的希望开发人员能给出一个更详细的描述，但是嘿，我们只能希望)。检查上面突出显示的第一个和最后一个红色框，显示列 A、B 和 C 稍微向左移动。但是因为我们可以使用它，而且我们必须指定一个参数，所以让我们试一试…

…将它复制/粘贴到 Colab 中代码单元格的底部，给它一个注释，然后在代码单元格的最后一行插入一个 df.head()函数，以便在执行后检查数据:

![](img/dac48006198ae4062fb915bb095dac06.png)

用光标在当前代码单元格点击 Runtime，Run before:

![](img/55f5406ad156188b42437c5525305bd9.png)

这会将该代码单元之前的所有代码加载到内存中。

然后使用突出显示的播放图标手动执行该代码单元:

![](img/fb96001a5cf366b3382f2777c853f9fd.png)

我们的数据显示在底部面板中，没有报告任何错误，因此这两行已经通过应用新的 Pandas 支持的函数得到了修复。

我可以告诉你，在 sentdex 的这个教程中，只有这两行是唯一被否决的函数。我希望您能够无缝地遵循本文，并将这些原则应用到您的废弃函数中，以恢复您的 Python 代码。

如有任何问题或意见，请在回复区留言。快乐机器学习编码在 Colab…