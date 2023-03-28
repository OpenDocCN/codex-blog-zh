# 如何使用 Symfony messenger 消费 RabbitMQ 消息(附示例)

> 原文：<https://medium.com/codex/how-to-use-symfony-messenger-to-consume-rabbitmq-messages-with-example-1440da1ef68?source=collection_archive---------7----------------------->

## PHP 中事件驱动架构的实践方法

![](img/34d41481545a6d700a97a6a64965e598.png)

[史蒂夫·哈维](https://unsplash.com/@trommelkopf?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

我已经更详细地解释了什么是事件驱动架构。

如果你是这个概念的新手，需要阅读更多相关内容，请访问[事件驱动架构快速指南](https://anastasionico.uk/blog/a-quick-guide-to-event-driven-architecture)。

如果你已经知道了足够多，并想把你的手放在一些 Symfony 代码，这是你的职位。

在本文中，您将学习如何使用 PHP 使用存储在 RabbitMQ 中的消息。

我们将通过 Symfony Messenger 消费它。

![](img/6aea16598d71eebcc533644715448159.png)

照片由 erica steeves 在 Unsplash 上拍摄

# 什么是事件驱动架构？

系统的传统架构是一个典型的请求/响应架构。

一个服务执行一个涉及其他服务的动作。

当第二个做它的事情时，第一个需要等待。

这很有效，但不是构建系统的最佳方式。

在[事件驱动架构](https://ably.com/blog/introduction-event-driven-architecture)中，流程遵循事件模式。

当事情发生时，例如，一个需要动作的事件，**我们存储它而不是处理它。**

这样，我们正在交互的服务确实需要停下来等待。

第二个可以检索事件并在准备好的时候执行操作。

**为了能够使用这个架构，我们需要在某个地方存储这个事件。**

有几个活动经纪人。

最著名的是 *Apache Kafka、Google Cloud Pub/Sub、RabbitMQ。*

![](img/86130b93106a12c89a678dfd2a3163e0.png)

Volodymyr Hryshchenko 在 Unsplash 上拍摄的照片

# 兔子 q

RabbitMQ 是最受欢迎的开源消息代理之一。

它支持许多消息协议。

我们可以用多种配置来部署它。

它还可以在许多操作系统和云环境中运行。

**它的工作方式是通过创建可以包含许多队列的不同通道。**

反过来，这些队列可以存储许多事件或消息。

我们在本文中使用 [RabbitMQ](https://www.rabbitmq.com/) 的方式是存储消息。

然后我们将使用 PHP 应用程序检索这些消息。

如果你想学习更多关于这门语言的知识，请阅读[PHP 基础知识](https://anastasionico.uk/blog/php-basics)

因为程序员不想一直重复发明轮子。

我们将为我们的应用程序使用一个 PHP 框架。

本文选择的框架是 Symfony。

![](img/26fa10e36e2f06a47a2c64a7e2c0dab7.png)

本·格里菲斯在 Unsplash 上拍摄的照片

# Symfony

Symfony 是目前最流行的 PHP 开发框架之一。

它由具有各自用途的组件集组成。

通过将所有这些组件放在一起，我们不需要担心配置。

诸如授权、认证、路由等。

[**Symfony**](https://symfony.com/) **已经将它全部内置在应用程序里面。**

我已经写了一些关于 Symfony 的指南，如果你想开始使用它，这里有[如何在你的机器上安装 Symfony 6](https://anastasionico.uk/blog/how-to-install-symfony-6)

Symfony Messenger 是这些组件中的一个，也是本文需要的一个。

# 如何使用 Symfony 消费 RabbitMQ 消息

Symfony 使开发人员能够轻松地创建一个能够从代理检索消息的结构。

在我们的例子中，我们只需要 4 块就可以完成任务。

我们需要:

*   **消息类是保存消息数据的类**；
*   **handler 类负责读取消息**。它还将把动作委托给一个或多个服务；
*   序列化器类是信息被翻译成可读版本供我们的应用程序使用的地方；
*   传输是我们发送和接收消息所需要的；

## 消息类

首先，我们想要的是来自 RabbitMQ 的消息。

为了让每个人都清楚这个例子，我们将在我们的对象中使用一个项目，

这将是一个 order_id 及其值。

```
{'order_id': 1234567}
```

为了处理这种格式，我们需要一个带有订单的 setter 和 getter 的类。

```
namespace App\Message;

class PurchasesNotification
{
    private int $order_id;

    public function getOrderId(): int
    {
        return $this--->order_id;
    }

    public function setOrderId(int $order_id)
    {
        $this->order_id = $order_id;
    }
}
```

很简单，不是吗？

## 处理程序类

处理者是奇迹发生的地方。

在 Symfony 中，**处理程序是 PHP 可调用的。**

调度该消息时调用的就是该类。

handler 类读取上面的 PurchasesNotification 类。

然后，它可以执行任务或将任务委托给适当的服务。

```
namespace App\MessageHandler;

use App\Message\PurchasesNotification;
use Symfony\Component\Messenger\Attribute\AsMessageHandler;
use App\Service\PurchasesService;

#[AsMessageHandler]
class PurchasesHandler
{
    public function __construct(public PurchasesService $service)
    {}

    public function __invoke(PurchasesNotification $message)
    {
        $orderId = json_decode((string) $message--->getOrderId(), true);

        try {
            $this->service->getPurchases($orderId);
        } catch (\Exception $exception) {
            echo $exception->getMessage();
        }
    }
} 
```

在本例中，我所做的是从消息类的`getOrderId()`方法中获取订单 ID。

然后我调用一个 PurchasesService 类来处理它。

![](img/36842ea4b1a7d07a0277444e0fa71938.png)

由 Kutan Ural 在 Unsplash 上拍摄的照片

## 序列化程序类

不幸的是，这个代码还没有准备好工作。

原因是 PHP 不能用它的标准序列化器来序列化它。

```
Could not decode message using PHP serialization {...}
```

为了解决这个问题，我们需要创建自己的序列化类。

此类必须具有 MessageSerializerInterface，并包含 2 个方法:

第一个将从传入的 Rabbit 消息中获取消息体和消息头，并创建一个我们的应用程序可以读取的信封。

后者使用 Envelope 的这个实例来获取消息，并以可用的格式返回一个包含数据的数组。

```
namespace App\Serializer;

use App\Message\PurchasesNotification;
use Symfony\Component\Messenger\Envelope;
use Symfony\Component\Messenger\Exception\MessageDecodingFailedException;
use Symfony\Component\Messenger\Transport\Serialization\SerializerInterface as MessageSerializerInterface;
use Symfony\Component\Serializer\SerializerInterface;

class ExternalMessageSerializer implements MessageSerializerInterface
{
    public function __construct(private SerializerInterface $serializer)
    {}

    public function decode(array $encodedEnvelope): Envelope
    {
        $body = $encodedEnvelope['body'];
        $headers = $encodedEnvelope['headers'];

        try {
            $message = $this--->serializer->deserialize($body, PurchasesNotification::class, 'json');

        } catch (\Throwable $throwable) {
            throw new MessageDecodingFailedException($throwable->getMessage());
        }

        $stamps = [];
        if (!empty($headers['stamps'])) {
            $stamps = unserialize($headers['stamps']);
        }

        return new Envelope($message, $stamps);
    }

    public function encode(Envelope $envelope): array
    {
        $message = $envelope->getMessage();
        $stamps = $envelope->all();

        if ($message instanceof PurchasesNotification) {
            $data = [
                'order_id' => $message->getOrderId()
            ];
        } else {
            throw new \Exception(sprintf('Serializer does not support message of type %d.', $message::class));
        }
        return [
            'body' => json_encode($data),
            'headers' => [
                'stamps' => serialize($stamps)
            ]
        ];
    }
}
```

## 交通工具

传输由 RabbitMQ 的配置组成。

我们告诉 Symfony 如何处理传入的消息，以及一旦该消息成为我们可以在系统中管理的类的实例时该做什么。

Symfony 还不知道如何得到这个消息。

简而言之，我们需要告诉它 RabbitMQ 存在，并且它准备好发送消息。

我们这样做的方法是通过`config/packages`目录中的 messenger.yaml 文件。

```
framework:
  messenger:
    serializer:
      default_serializer: App\Serializer\ExternalMessageSerializer

    transports:
      external_messages: '%env(MESSENGER_TRANSPORT_DSN)%/queue_order'
      serializer: App\Serializer\ExternalMessageSerializer

    routing:
      'App\Message\PurchasesNotification': external_messages
```

现在，Symfony 知道一个带有该 DNS 的外部消息正在等待它。

`MESSENGER_TRANSPORT_DSN`环境变量如下所示。

`MESSENGER_TRANSPORT_DSN=amqp://guest:guest@rabbit:5672/%2f`

Symfony 现在也知道，在信息到达后，它必须使用我们定制的`ExternalMessageSerializer`来翻译它。

并且消息的格式与`PurchasesNotification`类的格式相同。

从这里我们可以添加更多的信息，但是对于这篇基础文章，我宁愿保持简单。

## 听信息

既然拼图的所有部分都已完成，我们就可以运行监听 RabbitMQ 的命令了。

假设我们的传输名称是 external_messages，这就是我们需要消费的内容。

从终端，cd 到您的项目的根目录，并键入以下命令:

`bin/console messenger:consume external_messages`

一切都应该成功的。

您的消息应该已经从代理队列中消失。

事实上，服务已经处理了它们。

# 结论

您已经看到了如何让 Symfony 应用程序从消息代理接收消息。

事件驱动架构在数据的安全性和可靠性方面有很多优点。

而且使用这个 PHP 框架也很容易到位。

毕竟，我们只需要创建或更新 4 个文件。

你曾经在你的应用程序中使用过事件驱动架构吗？

如果你想了解更多关于这个主题和其他 PHP 文章，请点击本页的按钮

*最初发布于*[*https://anastasionico . uk*](https://anastasionico.uk/blog/consume-rabbitmq-messages-with-symfony-messenger)*。*