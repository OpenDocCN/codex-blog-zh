# 基于 K 均值聚类算法的客户细分

> 原文：<https://medium.com/codex/customer-segmentation-using-k-means-clustering-algorithm-4c125d29d11d?source=collection_archive---------4----------------------->

## 在 Python 中使用 Numpy、Pandas、Matplotlib、Seaborn 和 Scikit-learn 库

![](img/fb12d9dc0f85ad290a075a08963307d8.png)

照片由[布鲁克·拉克](https://unsplash.com/@brookelark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/@brookelark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

在这篇博文中，我们将使用被称为 **K-Means** 的**无监督**机器学习算法，使用 **Python** 上的各种库，如`numpy`、`pandas`、`matplotlib`、`seaborn`、`sci-kit learn`，来执行**客户细分**。

**客户细分**是指根据共同的品质将消费者分成不同的群体，以便企业能够高效地向每个群体销售产品。

**无监督学习**是一种机器学习，其中训练数据在没有任何预先分配的标签或分数的情况下提供给算法。因此，无监督学习算法必须首先自我发现训练数据集中任何自然存在的模式。

**K-means** 聚类是一种旨在将 n 个观测值划分为 K 个簇的方法，其中每个观测值属于具有最近均值的簇。

想了解更多关于 k-means 算法的工作原理，请看这篇文章

[](https://towardsdatascience.com/k-means-clustering-algorithm-applications-evaluation-methods-and-drawbacks-aa03e644b48a) [## k-均值聚类:算法、应用、评价方法和缺点

### 使聚集

:算法、应用、评估方法和缺点 Clusteringtowardsdatascience.com](https://towardsdatascience.com/k-means-clustering-algorithm-applications-evaluation-methods-and-drawbacks-aa03e644b48a) 

以下是项目的分步大纲:

1.  导入所需的库。
2.  导入数据。
3.  数据预处理和分析。
4.  识别数据中的聚类数。
5.  训练模型。
6.  可视化数据中的不同聚类。

这个项目的数据可以在[这里](https://www.kaggle.com/vjchoudhary7/customer-segmentation-tutorial-in-python)找到。

这个项目的完整代码可以在这里找到。

# 导入所需的库。

让我们导入这个项目所需的所有库。请注意，在您可以导入它们之前，必须安装所有的软件包。

`numpy`是一个拥有大量高级数学函数的库，这些函数对数组进行操作。

`pandas`是一个带有数据操作工具的库，构建在 numpy 的现有功能之上。

`matplotlib`是一个有绘图工具的库。

`seaborn`是一个建立在 matplotlib 之上的库，它提供了一个高级接口来绘制引人注目的情节。

`sklearn`是一个拥有机器学习工具的库。

# 导入数据

让我们读取数据并检查它是否已成功导入。

pandas 的 **read_csv** 函数让我们读取 csv 文件并将它们存储在数据帧中。让我们也分配一个变量 **df** 来存储数据帧。

让我们检查一下数据导入是否正确。我们可以使用 head 方法。 **head** 方法让我们看一下数据帧中的前五条记录。

既然数据已经正确导入。我们可以进入下一步的数据分析。

# 数据预处理和分析

现在，让我们研究一下我们收集的数据，并检查是否需要进行任何预处理。

## 记录和列的数量

让我们检查数据集中的行数和列数。我们可以用**形状**的方法。

从输出中我们可以看到，数据集中有 **200 条记录**和 5 个**列**。

## 关于列的信息

让我们试着理解我们正在处理的列，并检查它们的数据类型。我们可以使用**信息**方法。

我们可以看到有 5 列:
1) **客户 ID** :这是分配给每条记录的唯一的**整数** id。

2) **性别**:是一个**字符串**变量，用来表示一个人的性别。

3) **年龄**:是一个**整数**变量，用来表示一个人的年龄。

4) **年收入(k $ )** :这是一个**整数**变量，用来表示一个人每年以 1000 美元计的收入。

5) **消费分数(1–100)**:这是一个**整数**变量，用来表示一个人在那个商场花了多少钱:1 为最低，100 为最高。

## 缺失值检查

让我们看看数据集中是否有丢失的值。

我们的数据集中没有丢失任何值。

## 离群点检测

离群值是与其他数据点显著不同的数据点。这可能会在统计分析过程中导致严重的问题。因此，如果存在任何异常值，则需要清理数据。

我们可以使用**箱线图**方法来检查异常值。

图上的点表示异常值。我们只看到年收入方框图中的一个点。由于离群值的数量很少，我们可以继续进行分析。

## 统计信息

接下来，让我们使用 **describe** 方法来查看数据集中整数列的一些基本统计信息。

**CustomerID** 列的最小值为 1，最大值为 200，这表明该列似乎是正确的。

我们数据集中的人的平均**年龄**是 38.85 岁。

平均年收入为 60.56 千美元。

**消费得分**的最小值为 1，最大值为 99。这表明收集的数据没有任何错误。

## 逛商场的男性与女性人数对比

让我们绘制一个计数图来可视化雄性和雌性的计数。我们可以使用 seaborn 的 **countplot** 方法。

我们从图像中看到，逛这个商场的女性比男性多。

## 仅选择年收入和支出分数进行进一步分析

在可用的 5 列中，我们只选择 2 列来训练模型。如果我们选择所有 5 列，就很难可视化一个 5 维的图。然而，通过只选择两列，我们可以绘制一个二维图并有效地可视化它。请注意，我们可以使用 PCA 等降维技术来选择和可视化所有 5 列。

# 识别数据中的聚类数

我们将使用肘方法来为算法确定正确的 k 大小。

**肘法**是为模型选择最佳 k 值的最常用方法之一。

它通过计算不同 k 值的**组内误差平方和(WSS )** 来工作，并选择 WSS 减小最多的 k 值。

让我们细分 WSS:

1.  每个点的平方误差是该点和预测聚类中心之间距离的平方。
2.  可以使用任何距离度量，例如欧几里德距离或汉明距离。
3.  WSS 分数是所有数据点的这些平方误差的总和。
4.  WSS 分数显著下降的点被选为 k 的值

了解更多关于 WSS 的信息。查看这篇文章。

[](/@ODSC/unsupervised-learning-evaluating-clusters-bd47eed175ce) [## 无监督学习:评估聚类

### k 均值聚类是一种无监督统计学习的划分方法。这有点不像…

medium.com](/@ODSC/unsupervised-learning-evaluating-clusters-bd47eed175ce) 

让我们绘制肘图，并确定我们的问题的最佳集群数量。

从肘形图中，我们可以看到当聚类数= 5 时的肘形。因此我们可以选择 k 的值为 5。

# 训练模型

如上所述，对于手头的任务，我们将使用聚类数=5 的 k-means 算法。为此，我们将使用 **scikit-learn** 库。

我们用 **n_clusters=5** (聚类数)初始化 k-means 模型，我们使用 **k-means++进行初始化。k-means++确保了更智能的质心初始化，提高了聚类质量。 **random_state** 用于设置随机发生器的种子，以便结果可以被复制。**

要在 **scikit-learn** 中阅读更多关于 k-means 实现的内容，请使用以下链接。

[](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html) [## sk learn . cluster . k means-sci kit-learn 0 . 24 . 2 文档

### k-均值聚类。了解更多信息。参数 n_clustersint，default=8 要形成的簇的数量以及…

scikit-learn.org](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html) 

# 可视化数据中的不同聚类

让我们绘制或**散点图**来可视化数据点及其分配的聚类。

散点图显示数据点被清晰地分成 5 个不同的簇。

青色星团的年收入在 0 到 4 万美元之间。他们的消费得分很低。因此，他们成为商场的潜在机会，可以通过给予特殊折扣来锁定目标。

**黄色星团**的年收入也与青色星团相同。然而，他们的消费得分很高。所以他们可以被认为是理想群体。

**绿色集群**年收入高，但消费得分低。这表明他们去了商场，但没有花钱买东西。它们也是购物中心瞄准的潜在机会。

**红簇**年收入高，他们的消费分数也高。随着时间的推移，商场必须确保他们的利益得到满足。

**总结**

下面总结了我们为解决客户细分问题而实施 k-means 算法的逐步过程:

1.  我们导入了所需的库。
2.  我们导入了数据。
3.  我们对数据进行预处理和分析。
4.  我们确定了数据中的聚类数。
5.  我们训练了这个模型。
6.  我们将数据中不同的聚类可视化。
7.  就购物中心如何提高收入给出了见解。

# 未来的工作

这里有一些扩展项目的方法。

*   我们仅使用两个 2 列来训练模型。多种不同色谱柱的组合可用于获得更深入的见解。
*   不同的机器学习模型，如 **DBSCAN** 或 **BIRCH** 可用于训练模型。
*   该算法可用于不同的数据和其他问题，如开发推荐系统。

# 参考

*   [K-means 聚类:算法、应用、评估方法和缺点](https://towardsdatascience.com/k-means-clustering-algorithm-applications-evaluation-methods-and-drawbacks-aa03e644b48a)
*   [无监督学习:评估聚类](/@ODSC/unsupervised-learning-evaluating-clusters-bd47eed175ce)
*   [熊猫](https://pandas.pydata.org/docs/user_guide/index.html)
*   [Seaborn](https://seaborn.pydata.org/tutorial.html)
*   [Scikit-learn: K-Means 聚类](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)