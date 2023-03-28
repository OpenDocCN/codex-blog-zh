# 从数据工程师起步(以及如何在 Spark 中的整个数据帧上应用()正则表达式替换)

> 原文：<https://medium.com/codex/starting-out-as-a-data-engineer-and-how-to-apply-a-regex-replace-on-an-entire-dataframe-in-b60f5f37624c?source=collection_archive---------5----------------------->

![](img/0bb0937a025276788b97436ecc906053.png)

在外面我在工作，但在里面我快死了

> 如果你想直接找到真正简单的解决方案，那就去看看文章底部附近的**对数据帧部分**执行正则表达式(Regex)替换操作。

## 你可能想知道我是怎么来到这里的…

最近，我的老板让我做一件非常简单的事情。

“嘿，JL！我想让你看一下我们的顾问试图修复的代码。你真的应该删除我们数据中的双引号，但他们说这需要几周时间。”

因此，我开始尝试寻找一种在整个数据帧上应用*正则表达式替换函数的方法。*

## 关于我和我的工作的一些背景

我对我的工作不熟悉，刚来不到一个月。我开始尽我所能地学习关于数据工程、数据砖、Azure 等等的一切。我曾在发电厂处理物联网数据，这对我来说非常陌生。我确实有一个舒适的开发背景。在疫情开始时，我提高了我的 Javascript 和 Python，所以我至少对数据操作感到满意，尽管在大数据环境中这样做有点不同。

## 那么，如何才能从数据工程入手呢？

无论如何，如果你是从数据工程和大数据的东西开始，只要阅读并练习你可以用 **Spark** (PySpark，熊猫对 Spark 的东西)做的不同事情，据我所知，你已经有了一个很好的开始。Databricks 平台也有社区版，所以你可以使用它。这只是一个起点。也许，如果以后我有更多的时间，我可以更深入的用什么资源去看看。

## “兔子洞”

所以，如果你开始在谷歌上搜索 How*“How to execute regex operation on data frames”*，你会看到很多答案，它们看起来是非常简单的解决方案。但是，我可能“过度阅读”了搜索结果，并开始看到为什么你**不应该**使用像 ***apply()、applymap()、transform()*** 这样的东西，因为性能问题。我想对此感到焦虑是合理的，这将是我在生产流水线上实现的第一个代码片段，我的老板非常强调要比顾问做的更快。因此，我开始尝试基于我在 **StackOverflow** 上看到的东西实现许多非常复杂的代码，并在两天的时间里做了许多疯狂的事情。最后我又回到了只使用***apply()****(***apply()***好像比 ***applymap()*** 快了不少，所以只是注意一下)。为什么我又回去用 ***apply()*** ？嗯，我的解决方案是*过度工程化，*而且它甚至都不快。另外，我在使用 ***apply()*** (以及 ***applymap()*** 和***transform()****)时遇到了麻烦。我可以看到 ***apply()*** 不会给我合适的输出，这是因为我在一个系列上使用了***【replace()***方法(这个问题的解决方案在下一节中)。**

## **对数据帧执行正则表达式(Regex)替换操作**

**感谢 **Tambayan404 discord 社区**和我最好的朋友的 kuya(菲律宾语中“兄弟”的意思)帮助我弄清楚我需要传递 ***str*** 属性，并帮助我更好地理解 ***替换*** 的方法。(感谢 kuya 梳理)**

> **请注意，我摄取了我的数据，它最初是一个 **Spark 数据帧(df)** ，所以我也转换成一个 **pandas-on-spark 数据帧(psdf)** ，以便用于 ***。运用()*** 的方法去工作。**

```
**#importsimport pandas as pd
import numpy as np
import pyspark.pandas as ps
from pyspark.sql import SparkSession# Creating a spark dataframe from the CSV file# File location and type# Note, replace the [] with actual stuff
file_location = "[fill this with your file location]"
file_type = "CSV"# CSV options
infer_schema = "[true or false]"
first_row_is_header = "[true or false]"
delimiter = ","# The applied options are for CSV files. For other file types, these will be ignored.
df = spark.read.format(file_type) \
  .option("inferSchema", infer_schema) \
  .option("header", first_row_is_header) \
  .option("sep", delimiter) \
  .load(file_location)# Creating a pandas-on-spark dataframe from spark dataframe
psdf = ps.DataFrame(df)
display(psdf)# Perform an apply on the pandas-on-spark dataframe# Instead of simply passing x to the lambda, you must access the .str property of the Pandas Series that is taken as a parameter by the lambda function
# For beginning and end of string only
psdf2 = psdf.apply(lambda x: x.str.strip(‘\”’))
# For whole string
psdf3 = psdf.apply(lambda x: x.str.replace(r’\”’, ‘’, regex=True))display(psdf2)
display(psdf3)**
```

**![](img/0bb0937a025276788b97436ecc906053.png)**

**运行这段代码并显示 *psdf2* 和 *psdf3* ，您应该会看到 *psdf2* 在整个数据帧中的字符串的开头和结尾不再有双引号。而在 *psdf3* 中，你应该看到所有的双引号都被完全删除了。当然，您可以将 lambda 函数替换为您想要在数据帧上应用的任何转换(并将其保存到一个新的数据帧中！).**