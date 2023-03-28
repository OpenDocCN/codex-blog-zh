# 创建 ML 模型并将其作为 RESTful API:第 1 部分

> 原文：<https://medium.com/codex/creating-an-ml-model-and-serving-it-as-a-restful-api-part-1-4c8f7ed5e6ba?source=collection_archive---------4----------------------->

我开始在当地一家专注于数据科学和机器学习的公司实习，作为我培训的一部分，我的任务是创建一个简单的虹膜分类模型，根据给定的花瓣和萼片宽度和长度识别虹膜类别的类型(杂色、海滨和刚毛)。作为一个只有很少机器学习背景的人，我需要大量的研究和阅读，以达到所需的输出，但这一切都是值得的。

![](img/a302088f179f08536053d6658afe530f.png)

图片来源:[https://pixabay.com/images/id-3199253/](https://pixabay.com/images/id-3199253/)

在这篇文章中，我将分享我使用 Python 创建虹膜分类模型并通过 Flask RESTful API 提供该模型的步骤。我还创建了一个连接到 API 的简单 PHP 接口来演示它的用法。

# 模型创建🤖

在机器学习中，使用一组数据来训练模型，以识别某些模式。Python 的开源机器学习库 [Sci-kit Learn](https://scikit-learn.org/stable/) ，为监督和非监督学习提供模型训练。它使用简单，并且有很多教程可以开始使用。

首先，使用 pip 安装 Sci-Kit 库:

```
pip install -U scikit-learn
```

安装完成后，创建一个名为“model.py”的文件，其中可以使用 Sci-Kit Learn 已经制作的 Iris 数据集:

```
from sklearn import datasets
iris = datasets.load_iris()
```

*注意:您可以使用自己的数据集，但需要对其进行预处理和格式化，以便 Sci-Kit Learn 尽可能高效地理解和训练模型。

接下来，数据集被分成**数据和目标**。**数据**由花瓣和萼片的厘米宽度和长度组成，而**目标**或标签是要预测的类。对于虹膜分类，目标由杂色、海滨和刚毛组成。

```
iris_data = iris.data
iris_target = iris.target
```

在训练模型之前，数据和目标被再次分割，以便另一半用于实际的模型训练，而另一半用于验证模型(换句话说，测试模型的准确性)。

这可以通过使用 train_test_split 来实现:

```
from sklearn.model_selection import train_test_split
iris_data_train, iris_data_test, iris_target_train, iris_target_test = train_test_split(iris_data, iris_target, test_size = .5)
```

test_size 参数指定测试数据集的大小。

现在，我们可以开始构建模型了。有许多分类算法可以用于这个模型，但是我选择了 K-最近邻，因为它的准确度要高得多。

```
from sklearn import neighbors
classifier = neighbors.KNeighborsClassifier()
```

由此，我们可以开始训练模型:

```
classifier.fit(iris_data_train, iris_target_train)
```

我们做到了！我们有一个经过训练的虹膜分类模型。我们可以使用 accuracy_score 打印模型的精度级别:

```
from sklearn.metrics import accuracy_score
predict_iris = classifier.predict(iris_data_test)
print(accuracy_score(iris_target_test, predict_iris))
From the above, the model has an accuracy score of 97%! Great!
```

# 创建烧瓶 API ⚙️

在我们创建 Flask API 之前，需要使用名为 [Pickle](https://docs.python.org/3/library/pickle.html#:~:text=%E2%80%9CPickling%E2%80%9D%20is%20the%20process%20whereby,back%20into%20an%20object%20hierarchy.) 的 Python 库对模型进行序列化。通过“酸洗”python 代码，它被转换成字节流，以便于通过网络发送或保存在磁盘上。“拆包”则相反，它将 python 文件转换成其原始状态。

![](img/cf208296d1701bb88e2b1a97dfc358c4.png)

图片来源:【https://pixabay.com/images/id-1520638/ 

Pickle 可以使用 pip 安装:

```
pip install pickle-mixin
```

我们的虹膜分类模型可以使用以下代码进行“腌制”或序列化:

```
import pickle
pickle.dump(classifier, open(“iris_model”, “wb”))
```

这将创建 iris_model 文件。如果你试图打开它，它会显示难以辨认的字符。

既然我们已经腌泡了我们的模型，我们现在可以开始与 Flask API！

像其他 Python 库一样，我们也安装了 flask:

```
pip install flask
```

然后，我们创建名为“app.py”的主应用程序文件。在该文件中，我们导入 Flask API 所需的库:

```
from flask import Flask, jsonify
from flask_restful import reqparse, abort, Api, Resourceapp = Flask(__name__)
api = Api(app)
```

然后，我们的虹膜分类模型被加载和“解钩”:

```
import pickle
loaded_model = pickle.load(open(“iris_model”, ‘rb’))
```

当我们稍后创建前端接口时，我们需要一种方法将用户输入发送到 Iris 分类 Flask API。为此，我们为 URL 创建参数，并将用户输入传递到那里。我们需要的输入是花瓣和萼片的宽度和长度，从这些信息中，模型可以预测我们所指的鸢尾花的类型。

我们使用以下代码创建参数:

```
parser = reqparse.RequestParser()
parser.add_argument(‘slength’)
parser.add_argument(‘swidth’)
parser.add_argument(‘plength’)
parser.add_argument(‘pwidth’)
```

一旦设置了查询，我们需要调用模型来根据给定的输入预测虹膜类型。然后，我们创建一个名为“requestIris”的类，它接受 URL 参数值，然后将其插入到模型中进行预测。预测将采用整数格式，因此 0 表示 setosa，1 表示 versicolor，2 表示 virginica。另外，注意返回的数据应该是 JSON 格式的。

完整的代码如下所示:

```
class requestIris(Resource):
   def get(self):
   args = parser.parse_args()
   slength= args[‘slength’]
   swidth = args[“swidth”]
   plength = args[“plength”]
   pwidth = args[“pwidth”]
   prediction = (loaded_model.predict([[slength, swidth, plength, pwidth]])).tolist()
   if(prediction[0] == 0):
      type=”setosa”
   elif(prediction[0] == 1):
      type=”versicolor”
   else:
      type=”virginica”
   serve_model = {“Iris Type”:type}
   print(serve_model)
   return jsonify(serve_model)
```

然后，我们将资源添加到我们的 API，并为资源创建一个路由:

```
api.add_resource(requestIris, ‘/classify’)
```

最后但同样重要的是，我们添加代码来运行我们的 Flask 应用程序:

```
if __name__ == ‘__main__’:
 app.run()
```

万岁！我们现在有了我们的虹膜分类 API！

API 可以在本地运行，也可以发布到 Heroku。我在这里发表了我的:

【https://iris-classification-model.herokuapp.com/ 

在下一篇文章中，我将写 PHP 前端接口来演示 API 的应用。敬请期待！

嘿，你好！如果你喜欢读我的文章，并且想请我喝杯茶🍵，*我将非常感谢您在*[https://www.buymeacoffee.com/andreagon](https://www.buymeacoffee.com/andreagon)的支持

祝你今天过得愉快！