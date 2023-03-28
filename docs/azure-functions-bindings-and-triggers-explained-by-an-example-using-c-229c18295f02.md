# Azure 函数——通过一个使用 C#的例子解释绑定和触发器

> 原文：<https://medium.com/codex/azure-functions-bindings-and-triggers-explained-by-an-example-using-c-229c18295f02?source=collection_archive---------25----------------------->

![](img/cdb76c8510fb9fbce3de9611b134c97a.png)

与其他服务/应用程序的自动化交互是使用 Azure 功能的主要好处之一。这对于用 Azure 函数实现业务逻辑至关重要。这些功能是由触发器和绑定提供的。

[](/codex/basics-of-serverless-functions-by-the-example-of-azure-functions-hands-on-2de0e9a13d03) [## 以 Azure Functions + Hands on 为例，介绍无服务器功能的基础知识

### 云计算的最大优势之一是能够打开和关闭您的计算资源…

medium.com](/codex/basics-of-serverless-functions-by-the-example-of-azure-functions-hands-on-2de0e9a13d03) 

## 概观

**触发器**定义函数如何执行。它们决定函数何时被调用和从空闲状态中唤醒。Azure 函数提供了各种不同的[触发器](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings?tabs=csharp)。例如:您可以使用它们在每次有新条目存储到 Cosmos DB 时触发您的函数。以下是一个不完整的列表:

*   HTTP/Webhook
*   卡夫卡
*   活动中心
*   事件网格
*   存储帐户(Blob、表、队列)
*   宇宙数据库
*   Azure SQL

**绑定**是实现与其他服务连接的有效方式。这些都有助于与 Cosmos DB、Kafka、Storage Accounts 等服务进行双向数据交换。绑定可以像输入绑定(in)、输出绑定(out)或两种方式(inout)一样工作。函数可以根据您的用例的需要从/向服务检索/发送。

## **声明式配置**

Azure Functions 提供的最大的时间节省之一是，你不需要手动实现这些触发器和绑定。您可以利用声明性配置方法。这使您的生活变得更加简单，并且为您提供了一个稳定的工具，这有助于您的解决方案的质量。

使用 C#开发函数，这种声明性实现是通过为函数方法使用 decorators 来完成的。让我们看一个绑定到 Azure 存储队列的输入的小例子。在本例中，当某个队列收到消息时，该函数被调用。这个事件触发这个函数，并通过它的输入绑定获取消息的内容。然后，该消息通过输出绑定被重定向到另一个队列。你可以在这里看到代码:

**触发器和入站绑定**定义如下:

```
Run([QueueTrigger("test-queue", Connection = "functionkafkatrigger2022_STORAGE")]string myQueueItem, ILogger log)
```

test-queue 是我们的输入队列的名称，Connection 是连接字符串的键。在本地开发时，该项的值存储在 local.settings.json 中，而在部署时，它保存在应用程序设置中。这个绑定只提供消息的内容，消息通过 myQueueItem 字符串触发函数。这个参数可以很容易地在下面的代码中使用，就像您可以在函数内部的两行代码中看到的那样。

**输出绑定**由下式定义:

```
[return: Queue("test-output", Connection = "StorageConnectionAppSetting")]
```

test-output 是输出应该写入的名称，Connection 也是连接字符串的键。向队列发送数据只需调用 return 语句即可。在这个例子中，我们从输入中得到的相同消息，只是被发送到名为 test-output 的队列中。

有了这个解决方案，现在只要函数部署在 Azure 中或者在本地机器上运行，写入测试队列的消息就会被复制到测试输出队列中。

# 结论

绑定和触发器提供了一种与不同服务/应用程序交互的简单方式。这使您能够专注于业务问题，因为实现到不同数据源的连接的重复工作已经从您这里抽象出来了。