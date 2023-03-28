# 在中创建欺诈检测微服务。NET 和 Vonage

> 原文：<https://medium.com/codex/create-a-fraud-detection-microservice-in-net-and-vonage-ec685cb0540a?source=collection_archive---------5----------------------->

![](img/ed3c245ba4028965e798c21d0037d2c8.png)

当你注册保险政策、抵押贷款等金融资产，甚至手机计划等服务时，你申请的公司需要确保你是一个真实的人。最重要的是，对于这些公司来说，有一种方法可以验证潜在客户给他们的电话号码是否是一个真实的、活跃的号码，以便在未来可以联系到。

Vonage 有一个神奇的电话[号码洞察 API](https://developer.vonage.com/api/number-insight) 可以帮助支持这种情况。作为一名开发人员，您可能需要构建一个独立的内部微服务来处理这个用例。

在本文中，您将学习如何使用 Vonage Number Insights API 和。NET 来构建一个可以提供这种功能的小型分布式服务。

# 密码

如果你想跳过，你可以在 GitHub 上查看本教程的[代码。](https://github.com/nexmo-community/vonage-api-dotnet-fraud-service)

# 先决条件

要开始，您需要:

*   的 IDE。NET 开发，如 Visual Studio、Visual Studio 代码或 JetBrains Rider
*   的最新版本。NET 已安装

# Vonage API 帐户

要完成本教程，您将需要一个 [Vonage API 帐户](http://developer.nexmo.com/ed?c=blog_text&ct=2021-08-17-create-an-anti-fraud-detection-microservice-in-net-and-vonage)。如果您还没有，您可以今天就[注册](http://developer.nexmo.com/ed?c=blog_text&ct=2021-08-17-create-an-anti-fraud-detection-microservice-in-net-and-vonage)并开始使用免费积分进行构建。一旦你有了一个帐户，你可以在 [Vonage API 仪表板](http://developer.nexmo.com/ed?c=blog_text&ct=2021-08-17-create-an-anti-fraud-detection-microservice-in-net-and-vonage)的顶部找到你的 API 密匙和 API 秘密。

![](img/941d1ebad7252085aa9c1e88f9dc18f8.png)

# 开始你的。网络工人服务

英寸 NET 中，工作服务类似于后台进程，通常会订阅消息总线/队列或轮询其他服务。在本教程中，您将构建一个微服务，它接受来自消息队列的消息，通过调用 Vonage API 处理它们，然后将响应消息发送回队列。

让我们通过在终端中执行以下命令来创建一个. NET worker 服务:

`dotnet new worker -o FraudService`

接下来，执行`cd ./FraudService`导航到`dotnet`命令为你创建的文件夹。

您需要安装 [Coravel](https://github.com/jamesmh/coravel) ，这将有助于在我们的工人服务中安排作业:

`dotnet add package Coravel`

您还需要安装 [Vonage C# SDK 客户端](https://github.com/Vonage/vonage-dotnet-sdk):

`dotnet add package Vonage`

最后，从项目中删除文件`Worker.cs`，因为你不再需要它了。

# 构建主要的欺诈检测逻辑

您将构建的小型(微型)服务将模拟通过事件总线或队列与输入和输出进行交互。通过 REST API 或另一个 RPC 方法直接公开功能可能会导致像[喋喋不休的服务](https://docs.aws.amazon.com/whitepapers/latest/microservices-on-aws/chattiness.html)、弹性较差的系统等问题。

为了简单起见，本教程将使用文件系统存储消息/事件。

创建一个名为`MockEventBus.cs`的文件，并用以下代码替换它:

这个类将给我们提供模拟发送和接收模拟分布式消息的能力。它有两个公共方法:一个将消息“发送”到特定的队列——实现为文件——另一个从给定的队列/文件中删除所有消息。

接下来，您将创建一个名为`Constants.cs`的文件，并用以下内容填充它:

这是我们需要向其发送/接收消息的两个队列的名称。

接下来，创建一个文件`CheckPhoneNumberInvocable.cs`。这个文件是我们应用程序的主体。从我们的队列中提取消息、调用 [Vonage Number Insight API](https://developer.vonage.com/number-insight/overview) 以及存储结果的逻辑都将封装在这里。

用以下内容替换`CheckPhoneNumberInvocable.cs`的内容:

在这个虚假的场景中，其他服务可能想知道给定的电话号码是否有合法的运营商，并验证该号码是在哪个国家注册的。掌握这些信息有助于针对欺诈账户、客户等做出决策。

让我们仔细看看代码！它从队列中提取消息/事件，其中每条消息都是要验证的电话号码。Numbers Insight API 为该数字发送的结果随后存储在另一个队列中。

这在分布式系统中很常见:微服务会将其最终结果发布到异步消息队列中。其他系统可以订阅这个队列，接收这些结果，并做他们需要做的任何事情。

这种方法允许每个服务是自治的和有弹性的。例如，如果某个特定的服务停止运行，它可以在恢复运行后开始处理它停止运行时放入队列中的消息。对于典型的 REST/RPC 方法，当服务关闭时，它根本无法接收消息或请求——这使得异步消息驱动方法更具弹性。

回到代码，您需要配置并将这个应用程序的不同部分粘在一起。将`Program.cs`替换为以下内容:

本教程使用 [Coravel](https://github.com/jamesmh/coravel) 来配置一种简单的方法，每十秒钟运行一次我们的`CheckPhoneNumberInvocable`类。它模拟轮询或订阅消息队列，您马上就会看到这一点。

# 测试您的微服务

在`Program.cs`中替换了 API 密钥和密码后，在终端中执行以下命令来运行应用程序:

当程序运行时，您将看到应用程序生成的两个新的`.json`文件。您可以查看`mock_event_bus_validated_phone_numbers.json`，查看所有已验证电话号码的信息。

在`Program.cs`中，有一种叫做`ConfigureTestPhoneNumbers()`的方法，你可以硬编码一些电话号码，以便在应用程序启动时立即进行测试。然而，在应用程序运行时，您也可以修改`mock_event_bus_phone_numbers_to_validate.json`并动态添加新的数字。这样做可以刺激新消息从队列中进入。

您可以用以下内容替换`mock_event_bus_phone_numbers_to_validate.json`的内容:

几秒钟后，看一看`mock_event_bus_validated_phone_numbers.json`来查看您选择验证的任何电话号码的结果！

# 结论

您已经成功创建了一个. NET 微服务，它可以验证电话号码并将该信息发送回多个消费者/服务。每个消费者可以自己决定如何使用这些结果。您还理解了使用事件驱动的方法如何保持系统的各个部分的解耦和自治。

Numbers Insight 的 API 还有许多其他奇妙的功能。虽然本教程使用了“标准的”Numbers Insights API，但是还有一个更高级的特性[可以让您获得更多关于给定电话号码的信息。有时间试试吧！](https://developer.vonage.com/api/number-insight?theme=dark#getNumberInsightAsync)

*最初发表于*[*【http://github.com】*](https://gist.github.com/23060d5226eeb3379076247daaa32c2c)*。*