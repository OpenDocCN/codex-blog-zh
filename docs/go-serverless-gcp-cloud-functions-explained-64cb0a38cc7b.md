# 去无服务器:GCP 云功能，解释

> 原文：<https://medium.com/codex/go-serverless-gcp-cloud-functions-explained-64cb0a38cc7b?source=collection_archive---------15----------------------->

## 以及如何构建和(本地)测试您自己的无服务器 Python 函数

![](img/4f2ca2784738adb655ba1c390067ae41.png)

由 [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

谷歌云平台提供了所有行业标准的云处理工具，包括存储和计算，并且是我选择的数据科学任务的云提供商(即因为生态系统与其他 GCP 工具，如 BigQuery 或 Airflow，提供直观的数据工程和 MLOps 工具)。它还为 AWS Lambda 提供了一种替代方案，这种服务提供了托管无服务器功能的能力，称为云功能。

## “无服务器”是什么意思？

“无服务器”实际上并不意味着您的代码在没有服务器的情况下运行。事实上，您可以在本地服务器上运行无服务器功能(我们稍后会谈到这一点)。无服务器指的是这样一个事实，作为一个客户端，你不必每次想运行你的一些代码时都要安装和关闭一个服务器，你也绝对不必处理服务器的维护或初始化(云提供商处理这些)。相反，你可以编写一些代码，在每次向服务器发出*请求*时运行——大多数无服务器计算提供商的运行时间限制在 10 秒左右，偶尔他们会允许几分钟的异步调用。这意味着它们最适合于不需要存储任何数据的任务，只需要发出一个 POST 请求，对数据做一些事情，然后在几秒钟后关闭。

# 编写自己的无服务器函数

Cloud Functions 支持 Python，以及其他几种标准语言，尽管它像许多其他云无服务器提供商一样，也允许您指定自己的运行时。本教程的其余部分将涉及 Python，但是您可以很容易地将这些概念扩展到您选择的编程语言。

要进行本地测试，您只需要一个名为 **main.py** 的脚本，放在新文件夹**、**中，您可以在其中定义任何想要的函数。唯一的规定是函数*必须*返回一些东西，最好是一个 JSON 或者一段文本。例如，在 **main.py** 中:

```
def say_hello(event):
    name = request.args.get('name', None)
    if name is None:
        return f"Hi there!"
    else:
        return f"Hi, {name}!"
```

其中`request`是`Flask.request`对象。

接下来，你需要安装 *functions-framework* ，这是 GCP 为运行他们的云功能而开发的框架。在 Python 中，它实际上只是 Flask 的一个包装器，处理 POST 请求的请求信息。可以用`pip install functions-framework`安装。

现在在您的终端中运行`functions-framework --target=say_hello`。您的服务器现在正在运行，可能在 [http://localhost:8080 上。](http://localhost:8080.)

要发出 POST 请求，要么使用`curl`，要么编写第二个可以运行的 Python 脚本，如下所示:

```
import requests

params = {
    "name": "Neha"
}

print(requests.post("http://localhost:8080", json=params).content)
```

稍后，在云函数上，您将需要一个`requirements.txt`文件。我推荐通过`conda create <env_name>`创建一个新的`conda`环境，然后是`conda activate <env_name>`。这样，您就可以跟踪您必须为这个项目单独安装的模块，以及一个特定的 Python 运行时(如果您愿意指定的话)。要在最后导出需求，运行`pip list --format=freeze > requirements.txt`。

# 托管在 GCP 云功能上

你需要一个有效的谷歌云平台帐户才能使用云功能。一旦你注册了，在你的第一个项目的**产品**中查找“云功能”。按照步骤设置和部署您的第一个云功能！您可以从多个运行时中进行选择，或者使用一个定制的运行时，并使用内联代码编辑器或以. zip 格式上传文件。

记下一些权限:

1.  如果您希望您的 API 对公众可见，请确保您已经选择了**编辑** > *运行时、构建、连接和安全设置下的*允许所有流量*。*这确保了所有的流量实际上都能够*访问*你的功能。
2.  如果你想让所有的流量都能运行你的功能，你需要多一个权限:转到权限标签，然后点击授权访问添加一个新用户。新用户的名称应该是“allUsers”给他们“云功能调用者”的角色。

编码快乐！

Neha Desaraju 是德克萨斯大学奥斯汀分校学习计算机科学的学生。你可以在网上的[**estau dere . github . io**](https://estaudere.github.io/)**找到她。**