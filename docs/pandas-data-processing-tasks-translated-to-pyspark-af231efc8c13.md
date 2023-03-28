# 熊猫数据处理任务翻译成 PySpark

> 原文：<https://medium.com/codex/pandas-data-processing-tasks-translated-to-pyspark-af231efc8c13?source=collection_archive---------5----------------------->

![](img/04a7e9fe108924c83f149c36535c3f16.png)

由[约书亚·索蒂诺](https://unsplash.com/@sortino?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

我们生活在大数据时代。随着互联网的发展，数据以巨大的数量和高度的可变性快速增长。大数据处理可能会让你头疼，因为它自然需要大量的运行时间。Apache Spark(或 Spark)是处理大数据的流行工具之一。

Spark 是用于大规模数据处理的统一分析引擎。借助 Spark，我们可以快速执行数据处理，并将处理任务分配到多台计算机上。人们使用 Spark 是因为它可以在 Python、Scala、Java、R 和 SQL 等流行的编程语言中部署。它还有一堆支持流数据、机器学习和图形处理的库。

其中一个 Spark 接口是 PySpark，它允许您使用 Python APIs 编写 Spark 应用程序。PySpark 支持 Spark 的功能，如 Spark SQL、DataFrame、Streaming、MLlib(机器学习)和 Spark Core。作为一个经常和熊猫打交道的人，我发现 PySpark 可以做我在那里做的事情。然而，实现却大不相同。我会列出一些我平时在熊猫里执行的数据处理任务，翻译成 PySpark。

# **py spark 里的熊猫**

## 创建 Spark 会话

在我们开始使用 Spark 之前，我们需要创建 Spark 会话。这是用数据集和数据帧 API 编程 Spark 的入口点。从下面的代码可以看出，我们可以设置会话的应用程序名称。然后通过使用`getOrCreate`方法，创建一个 Spark 会话。

## **创建数据帧并读取文件**

熊猫提供了很多读取文件的方法。下面是读取逗号分隔值(CSV)文件的示例。

要在 PySpark 中执行类似的任务，我们可以做的是创建一个空的数据帧。之后，空数据帧与 CSV 数据联合。PySpark 数据帧在弹性分布式数据集(RDDs)之上实现，可并行操作。这样的实现使得 PySpark 转换数据的速度比熊猫还要快。

我们可以使用 PySpark 创建一个数据模式。Spark DataFrames 模式被定义为类型化列的集合。我们使用`StructType`方法来存储整个模式，使用`StructField`来定义各个列。我们还使用`StringType`和`IntegerType`来定义每一列的数据类型。然后，我们可以使用前面定义的模式，通过使用`CreateDataFrame`方法创建一个空的数据帧。之后，我们读取 CSV，然后将其与空数据帧合并。

## **数据转换**

PySpark 在一个名为`pyspark.sql.functions`的模块中提供了数据转换工具。这个模块存储了许多转换数据的函数方法，如转换为日期时间、连接列等。

接下来，我们将看到我在过去的项目中使用 PySpark 进行的一些数据转换，并与 Pandas 进行比较。

*   **将字符串转换为日期时间**

将字符串转换为日期时间是数据清理过程中的常见做法。目的是确保我们的数据有一个合适的日期格式。在熊猫身上，我们可以用`to_datetime`的方法。在 PySpark 中，我们可以使用来自`pyspark.sql.functions`模块的`to_timestamp`。从代码中，我们还可以看到，为了导航该列，我们需要使用 PySpark 中的`withColumn`方法。熊猫和 PySpark 写日期格式也有区别。

*   **获取时差**

下面的示例代码展示了 Pandas 和 PySpark 在获取到达时间和离开时间之间的差异。除了日期格式书写的不同，我们还可以发现 Pandas 和 PySpark 如何减去到达和离开时间的不同。在 PySpark 中，我们需要使用`unix_timestamp`方法来标准化日期格式。然后，由于`triptime`列以分钟的形式返回时差，在 PySpark 中，我们需要将减法结果除以 60，然后使用`IntegerType()`对象将其转换为整数，与之前创建模式的对象相同。

*   **连接列中的字符串**

在 PySpark 中，函数中有一个名为`lit`的方法来声明一个随机字符串。在本例中，我们使用它将分隔符`-`放在`source_airport`和`destination_airport`之间。然后用一种叫做`concat`的方法，把它们包装起来，得到航班的路线。

*   **条件列转换**

为了在 Pandas 中应用条件列转换，我们可以使用名为`where`的 NumPy 方法。在 PySpark 中，相当于调用`when`的`functions`方法来声明真值，调用`otherwise`来声明假值。

*   **删除列**

对于删除列功能，我们看不出 Pandas 和 PySpark 之间有太大的区别。在 PySpark 中，我们只需要将`*`放在被删除列的列表之前。

# 结论

从上面的例子中，我们可以看到 PySpark 在数据转换任务中可以做熊猫做的事情，从读取文件到删除列。当然，还有更多相当于熊猫的 PySpark 数据转换功能。我建议你参考 [PySpark 文档](https://spark.apache.org/docs/latest/api/python/)来探索更多的功能。

希望这篇文章对刚开始使用 PySpark 的你有所帮助，尤其是已经熟悉熊猫，玩大数据的你。要练习使用 PySpark，您可以在您的设备上设置 Spark。但如果你想消除在你的设备上设置 Spark 的复杂性，你可以随时使用 Google Colab，因为它支持 Spark。