# 用 Dagstd 简化 Dagster 代码库

> 原文：<https://medium.com/codex/simplifying-dagster-codebases-with-dagstd-b1c271f0aed5?source=collection_archive---------25----------------------->

不再是样板文件。

![](img/5e73827e3bb59060b1a1eea930bea2a4.png)

照片由[黛安·皮凯蒂诺](https://unsplash.com/@diane_soko?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

如果您在数据工程领域工作过一段时间，您可能需要使用某种编排或管道来创建完全自动、可靠的工作流。绝大多数公司都是使用 Apache Airflow 来实现这一点的。Airflow 非常强大并且得到了很好的支持，但是它有一个陡峭的学习曲线和相当大的复杂性。

出于这个原因，许多公司最近转向更现代的气流替代品，如 Prefect 和 Dagster，以快速有效地构建他们的数据管道。它们提供了更简单的 API 和强大的 web UIs 来管理和监控您的自动化操作，并提供了合理数量的集成来简化常见的数据任务。

就我个人而言，在领导我公司的数据编排项目时，在评估了几个不同的备选方案后，我们选择了 Dagster，它现在管理我们内部和外部报告的所有数据自动化。Dagster 是一个很棒的工具，但是有些时候你只需要传入一个简单的整数或者字符串作为 op 的输入，但是在 Dagster 中，op 的输入只能是其他 op 的输出。这导致编写了许多样板函数，它们只返回格式化的字符串，甚至只是一个整数。这就是 Dagstd 被创建的原因。

Dagstd 是一组与 Dagster 一起使用的常见操作和函数，它还包括一个 Sphinx Autodoc 扩展，允许自动拾取和记录操作，从长远来看，这为我节省了很多时间。

# Dagstd 操作

目前(从 v0.1.1 开始)，Dagstd 包含:

*   返回普通数字的简单操作
*   定值操作
*   数学和字符串运算的辅助操作
*   检索环境变量的操作
*   Sphinx autodoc 对 Dagster ops 的支持

当然，Dagstd 可以通过`pip`安装:

```
pip install dagstd
```

它是用`dagster==0.14.17`构建的，但应该也能与更高版本一起工作。

下面是一个纯 Dagster 图的例子，它下载一个每日的 ZIP 文件并提取一个已知的文件名。注意:为了简洁起见，省略了`download_large_file`操作。

这是相同的图表，具有相同的功能，但具有 Dagstd ops。

这是一个小例子，因此节省的空间可能不是很大，但是使用 Dagstd 可以避免多少样板文件。

# Sphinx Autodoc 插件

Dagstd 包含一个 Sphinx autodoc 插件，可用于为 Dagster ops 生成文档。要使用 autodoc 插件，请将以下内容添加到您的`conf.py`文件中:

```
extensions = [
    ...
    'dagstd.sphinx.parser',
]
```

默认情况下，这将为所有 op 文档加上前缀`(op)`。要更改这一点，请将以下内容添加到您的`conf.py`文件中:

```
dagstd_op_prefix = 'My Op'
```

你可以在 [Dagstd 文档](https://dagstd.readthedocs.io/en/latest/readme.html)中看到一个这样的例子。

# 捐助

Dagstd 是全新的，我也不是 Dagster 方面的专家，所以肯定有一些更常见的操作我可以包含在库中！如果你发现自己不得不在 Dagster 代码库中写很多东西，请告诉我，我会尽可能地添加它们。下一个增加的可能是处理日期的 ops，所以请告诉我你想要什么，或者在这里评论，或者在 GitHub 上打开一个问题或请求。

*   问题追踪:【https://github.com/isaacharrisholt/dagstd/issues 
*   源代码:[https://github.com/isaacharrisholt/dagstd](https://github.com/isaacharrisholt/dagstd)
*   文档:[https://dagstd.readthedocs.io/en/latest/readme.html](https://dagstd.readthedocs.io/en/latest/readme.html)

我希望至少你们中的一些人会觉得这个包有用，我很乐意听到你们的反馈！同时，为什么不通过查看我的另一个开源 Python 包 Quiffen 来进一步简化您的财务数据工作流呢？这是一个处理 Quicken Interchange Format (QIF)文件的好工具。你可以在这里了解更多信息:

[](https://towardsdatascience.com/parsing-qif-files-to-retrieve-financial-data-with-python-f599cc0d8c03) [## 使用 Python 解析 QIF 文件以检索金融数据

### Quiffen 包的基本概述以及它如此有用的原因

towardsdatascience.com](https://towardsdatascience.com/parsing-qif-files-to-retrieve-financial-data-with-python-f599cc0d8c03) 

如果你想联系我，你可以发电子邮件到 isaac@isaacharrisholt.com 或者 LinkedIn 或者 Twitter。快乐工程！