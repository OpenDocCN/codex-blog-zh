# 使用机器学习来提高你的问卷或计算器的准确性

> 原文：<https://medium.com/codex/using-machine-learning-to-increase-the-accuracy-of-your-questionnaire-or-calculator-de9a3f4486b6?source=collection_archive---------9----------------------->

在这篇文章中，我将帮助你在计算器或问卷调查中使用机器学习来了解更多关于你的用户的信息。读完之后，你会知道你需要的所有东西，用有根据的猜测来填充可选问题的空白，比如用户可能会做出什么样的回答。我使用 Python Pandas 来实现这一点，但是所有的概念都应该适用于您正在使用的任何语言。因此，如果您勾选了以下复选框，请继续阅读。

☑:你想提高计算器或测验结果的准确性

☑:它的预期用途是尽可能多地了解个人用户

您正在考虑实施的☑或它已经包含可选问题

☑:你以一种可以被看作是数据框的形式存储它的回答的历史

☑:你在机器学习方面有些经验

检查盒子还是只是好奇？太好了——我们开始吧！

![](img/188fdc0facff9807de24c40e22899492.png)

[https://lift off . io/blog/how-to-apply-machine-learning-to-re-engage-users-and-increase-ltv/](https://liftoff.io/blog/how-to-apply-machine-learning-to-re-engage-users-and-increase-ltv/)

为了你的方便，机器学习算法已经建立起来了。一旦你的数据准备好了，选择了正确的算法，构建模型和做出预测只需要 3-7 行通用代码。因此，您的大部分工作将包括选择最佳算法和超参数，而您的代码将致力于将数据转换成算法友好的格式。

# **选择算法**

你会像对待任何其他 ML 项目一样选择算法，但有一个明显的例外——不同的问题需要不同类型的变量。典型的过程相当复杂，依赖于无数因素，如数据的大小和质量，以及您是否重视准确性、输出的可解释性或速度。我推荐[遵循这个有用的指南](https://www.kdnuggets.com/2020/05/guide-choose-right-machine-learning-algorithm.html)。

由于不同测验问题的答案可能会有不同的形式，然而，在这种情况下，在采用连续数据和分类数据的算法之间进行选择可能会很棘手。分类问题接受有限数量的可能回答，例如从 0 到 10 的整数或字母 a 到 d。另一方面，连续回答可以采用任何浮点或“自由回答”字符串的形式。采用分类值的算法示例包括逻辑回归和朴素贝叶斯，而线性回归、决策树和大多数其他算法采用连续数据。我们将这些算法分别称为分类算法或连续算法。

**连续算法更加通用**

连续算法具有获取分类值的能力，但反之则不然。因此，如果您的问卷包含大量的分类答案和连续答案，选择连续算法将是有利的。

**自由回答问题很难处理**

你们中的大多数人应该简单地从训练集中删除自由回答问题，并在决定运行哪个算法时忽略它们。如果你的数据集很大，你的问卷包含大量的自由回答问题，并且你有必要的耐心和经验，那么你可以对自由回答的答案进行情感分析，从而采用连续算法。我不会在这篇文章中解释如何做到这一点。如果你想在这方面得到帮助，请在下面评论或给我发消息——我很乐意和你谈谈你的项目！

一旦你决定了一个算法，就该编码了！

下面是一些让您起步的代码:

```
import pandas as pd
import numpy as np
from sklearn import preprocessingdf = pd.read_pickle(r'C:\file_location.p')
```

# **准备您的数据**

我的数据框包含了太多可删除的列，所以我采取了创建一个相对较短的列列表的方法，这个列表被称为“features ”,我计划用它来构建我的模型。然后，我创建了一个名为‘new _ df’的新数据框来存储这些列。

```
new_df = df.filter(features, axis=1)
```

但是首先，您需要决定在“特性”中包含哪些列…

**数组/列表**

如果您的任何问题将其答案存储为统一大小的数组或列表，则元素可以分布在多个列中，并被视为单独的问题。下图说明了在给定一个包含大小为 2 的列表的问题的情况下，如何更新数据框和要素列表。

```
example_list_0 = []
example_list_1 = []
for ar in new_df[‘example_list’]:
    example_list_0.append(ar[0])
    example_list_1.append(ar[1])
new_df[‘example_list’] = example_list_0
new_df[‘example_list_1’] = example_list_1
features.append('example_list_1')
```

**不需要删除所有不相关的列**

没有必要过滤掉所有你认为不能很好地预测其他问题的问题，例如，眼睛的颜色不能很好地预测一个人的习惯，因为任何好的 ML 算法都会为你做这件事。此外，你永远不知道 ML 可能会发现什么有趣的相关性！

**深度相关和重复列**

我的数据框包含了每个用户对特定答案组的总计。类似地，直接相关的列对构建模型非常不利。也就是说，假设第三列包含 2 个问题的总数，并且在实现 ML 之前，当其中一个问题留空时，它被赋予一个任意值，例如零。

```
column_A + column_B = column_C
```

然后，训练数据将显示，如果用户将其中一个问题(例如，column_B)留空，则 column_A = column_C。然后，模型将预测所有空白都等于零，但实际情况并非如此。因此，应该删除所有形式的直接相关或重复的列。

您可能已经熟悉的一个相关问题是，在构建回归模型时，应该注意包含非常相关答案的列。这是因为使用 l1 标准构建回归模型将会丢弃非常相似的有价值的问题，例如鞋码和长度，即使它们是有价值的预测因素。这可以通过使用 l2 范数或删除其中一列来最容易地解决。

**坏数据或数据不足**

空白超过 50%的列也应该删除。随着数据大小的增加，可以调整这个百分比。此外，任何没有价值的列，无论出于什么原因，数据是错误的、有偏差的、对每个用户都是一样的或者不可读的，都应该被删除。

**道德考量**

另一件要考虑的事情是你的模型的道德性。如果由于道德原因，可能存在你不希望延续的相关性，那么你确实应该放弃这些问题。例如，你可能想让你的 ML 算法对种族、性别或宗教问题视而不见，这样它就不会“被诱惑”从这些因素中得出结论。

**既然你已经选择了你的专栏……**

您需要根据数据是采用分类值还是连续值来分别准备数据。

# **对于连续算法**

如果选择连续算法，则为只读。

**是或否问题**

“否”和“是”可以分别替换为 0 和 1。

```
new_df[‘example_question’] = 
    new_df.example_question.map(dict(YES=1,NO=0))
```

空值应填入该列中所有 0 和 1 的平均值。

```
new_df[['example_question']] = 
    df[['example_question']].fillna(new_df.example_question.mean())
```

**那‘也许’呢？**

“也许”可以简单地表示为数字 1，而“是”则表示为数字 2。重要的是，数字模糊地代表了类别的含义。也就是说，将“否”、“是”和“可能”分别排序为 0、1 和 2 是没有意义的，因为“可能”应该与比“是”更小的数量相关联。

**是非题**

熊猫自然把‘假’读成 0，把‘真’读成 1。这很好，但是如果您的列包含空值，那么您将希望找到一个平均值来填充它们。我能想到的找到这个平均值的最好方法是把 false 和 true 分别转换成 0 和 1，就像你用' no '和' yes '一样。

**任何其他分类问题**

连续算法只能理解数字上可量化的类别。也就是说，如果类别代表沿着一个范围采取步骤，例如“从不”、“很少”、“有时”等，那么它们可以分别用 0、1、2 等来表示。然后可以用平均值填充空值，与上面的情况类似。但是，如果类别之间没有数字关系，例如姓名列表，那么就不太可能存在有意义的数字表示，因此应该从训练数据中删除这些问题。

# **对于分类算法**

对于那些使用分类算法的人…

**是或否问题**

“否”和“是”可以分别替换为 0 和 1。

```
new_df[‘example_question’] = 
    new_df.example_question.map(dict(YES=1,NO=0))
```

空值应该用列中所有 0 和 1 的模式来填充。

```
new_df[['sample_question']] = 
    df[['example_question']].fillna(new_df.example_question.mode())
```

**那‘也许’呢？**

“也许”可以简单地用数字 1 来表示，把“是”推给数字 2 来表示。使用分类算法时，这些数字的顺序远不如使用连续算法重要。也就是把‘否’、‘也许’、‘是’分别排序为 0、1、2，会帮助人类记住哪个数字与哪个类别相关联，但对模型没有帮助。所以，随便把答案随意分配给数字。

**是非题**

熊猫自然把假读成 0，真读成 1。这太棒了！但是，把它们转换成 0 和 1 肯定不会有什么坏处，就像你对“是”和“否”所做的一样。空值可以类似地被非空的模式替换。

**任何分类问题**

所有类别都应附加一个整数。与上述情况类似，空值可以用模式填充。

干得好！您已经成功地准备好了数据，所以最困难的部分已经过去了。

# **机器学习**

遵循典型的 ML 流程…

1.  用 sklearn 的 train_test_split 把数据拆分成训练集和测试集。如果要选择超参数，则应该选择和验证集(如果要使用验证集来选择超参数)。然而，回过头来看，如果您处理的数据量类似地少，我可能会建议您使用交叉验证来拆分数据。

```
from sklearn.model_selection import train_test_split

train_features, test_features, 
    train_goal, test_goal = \
    train_test_split(temp_df[features], temp_df[goal], test_size=.2, 
    random_state=6)
```

如果您要选择超参数，那么在这一点上，您可以将您的训练集分成如下的训练和验证集。但是，如果您的数据较小，那么您应该考虑使用交叉验证。我将在下面的决策树案例中举例说明如何做。

```
train_features, validation_features, train_goal, 
    validation_goal = \
    train_test_split(train_features, 
    train_goal, test_size=1/8, random_state=6)
```

注意:此时，我遇到了一个奇怪的问题，我的训练、测试和验证集获得了 nan 值。我用下面的代码解决了这个问题，但是，由于我从来没有找到问题的根源，我怀疑这是处理它的最好方法。我很想听听你的相关经历和想法。

```
test_features = np.nan_to_num(test_features)
train_features = np.nan_to_num(train_features)
validation_features = np.nan_to_num(validation_features)
```

2.建立你的模型，做一个预测！

这里是一些线性回归和决策树模型的示例代码。

**线性回归**

```
from sklearn.linear_model import LinearRegressionscaler = preprocessing.StandardScaler().fit(train_features)
train_features = scaler.transform(train_features)
test_features = scaler.transform(test_features)
validation_features = scaler.transform(validation_features)

model = LinearRegression().fit(train_features, train_goal)
prediction = model.predict(test_features)
validation_prediction = model.predict(validation_features)
```

**决策树**

这里我包含了一些使用 6 重交叉验证来寻找超参数的代码。

```
from sklearn.model_selection import GridSearchCVhyperparameters = {‘min_samples_leaf’: [1, 10, 50, 100, 200, 300], 
    'max_depth’: [1, 5, 10, 15, 20]}
search =
    GridSearchCV(DecisionTreeClassifier(random_state=6), 
    hyperparameters,cv=6,return_train_score=True)
search.fit(train_features, train_goal)
print(search.best_params_)
```

我发现我的理想超参数是 max_depth=5，min_samples_leaf=50。

```
from sklearn.tree import DecisionTreeClassifierclf = DecisionTreeClassifier(random_state=1, max_depth=5,
    min_samples_leaf=50)
decision_tree_model = clf.fit(train_features, train_goal)
test_dec_pred = decision_tree_model.predict(test_features)
val_dec_pred = decision_tree_model.predict(validation_features)
```

3.将你的预测与另一个模型进行比较，或者在我的情况下，简单地将训练集的平均值(ML 之前我的计算器使用的数据)进行比较。

为此，您需要计算模型预测和次佳预测的均方根误差(RMSE)。

要计算平均值的 RMSE…

```
from sklearn.metrics import mean_squared_error

mean = train_goal.mean()
test_mean = pd.Series(mean for _ in range(test_goal.size))
mean_test_rmse = mean_squared_error(test_goal, test_mean) ** (1/2)
```

你的模型…

```
test_rmse = mean_squared_error(test_goal, test_pred) ** (1/2)
```

**测试精度**

最后，为了让我公司的其他部门相信我的模型对他们的气候计算器是一个有价值的补充，我展示了预测答案时误差减少的百分比。

```
test_percent_error_reduced = 1 - test_rmse / mean_test_rmse
```

我非常有趣地发现，一些模型比简单地使用平均值更难预测一些问题的答案。我推测这是由于缺乏数据和一些问题之间的相关性。所以，我没有在那些问题上实现 ML——简单！

恭喜你！您已经成功构建了未回答问题的机器学习预测器，并可以用数据支持您的预测能力。剩下的唯一一步是将你的模型应用到你的问卷结果中。

由于调查问卷的设置可能非常不同，而且没有人比你更了解你自己，所以我会给你一些指导性的建议。

根据您对新数据的重视程度、您拥有的数据量以及您希望用户多快收到结果，您可以控制构建新模型的频率。这可以从每次新的空白需要填充，到例如每 1000 个用户留下一个空白之后。这个数字可以根据不同的问题进一步定制。

随着您的数据随着时间的推移而变化，定期检查新算法是否比您当前的算法更相关，这可能是值得的。在这种情况下，您可以按照类似的过程再次浏览这篇文章。或者，自动化这个过程将是惊人的，我很想知道你是如何做到的！

感谢你在这个过程中坚持与我在一起——我希望你觉得这是值得的。如果你在这个过程中的任何一个步骤或实施过程中需要具体的帮助，我很乐意听到你的项目，我的公司 Permutable 提供咨询服务，所以请不要犹豫。