# 将 RabbitMQ 和 gocron 添加到您的数字海洋液滴中[第 2 部分]

> 原文：<https://medium.com/codex/add-rabbitmq-and-gocron-to-your-digitalocean-droplet-part-2-a0c0fc445ec7?source=collection_archive---------6----------------------->

## 配置 RabbitMQ

(这是 3 部分系列的第 2 部分。你可以在这里阅读第一部分

![](img/cdc5a34eebf2e4ce337e84915619343b.png)

照片由 [Daryan Shamkhali](https://unsplash.com/@daryan?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 开始之前

要使用 DigitalOcean droplet，您需要通过 SSH 连接到您的服务器。我建议你在这样做的时候创建一个新的管理员用户。如果你知道你在做什么，使用根用户是没问题的。如果您不确定，可以在参考资料部分找到一些有用的链接。

# 配置 RabbitMQ

让我们开始在我们的服务器上配置 RabbitMQ。RabbitMQ 通常随 DO 一起安装，这取决于您的 Linux 发行版的更新程度。

您可以通过运行`status`命令并检查输出来确认这一点:

```
$ sudo rabbitmqctl status | grep rabbit
.
.
.
{rabbit,"RabbitMQ",....}
```

如果 RabbitMQ 没有安装，只需运行:

```
$ sudo apt-get install rabbitmq-server
```

现在已经安装了 RabbitMQ，我们需要在建立连接之前创建一个用户。为此，我们将使用 RabbitMQ 的命令行管理工具`rabbitmqctl`:

```
rabbitmqctl add_user <USERNAME> <PASSWORD>
```

使用相同的工具，将**管理员权限授予您的用户**:

```
rabbitmqctl set_user_tags <USERNAME> administrator
```

在最后一步中，我们将向管理员用户授予权限。RabbitMQ 权限分为**配置、读**和**写权限。对于这个应用程序，允许授予 all 是可以接受的，但是在生产应用程序中这是一个安全风险。**

```
rabbitmqctl set_permissions -p / <USERNAME> ".*" ".*" ".*"
```

现在我们已经创建了用户，让我们将连接字符串保存在应用程序的环境变量中。为此我们将使用`.env`文件。将下面一行添加到应用程序的`.env`文件中:

```
AMQP_SERVER_URL=amqp://<USERNAME>:<PASSWORD>@localhost:5672/
```

看到这个`amqp`前缀了吗？它代表**“高级消息队列协议”**。这是 RabbitMQ 使用的默认协议。我们将把它和 [amqp 客户端库一起用于 go](https://pkg.go.dev/github.com/streadway/amqp) 。

在实现入队功能之前，让我们确认一下是否可以检查我们的队列:

```
$ sudo rabbitmqctl list_queuesTimeout: 60.0 seconds ...
Listing queues for vhost / ...
name	messages
```

因为我们还没有创建队列，所以我们不期望看到该命令列出的任何内容。

# 将消息排队

我们将创建一个处理消息生成的`enqueue`函数。如果你想在你的服务器上测试这个，你可以在你的 API 控制器中添加`enqueue()`函数调用。我使用`POST /fallback`端点来测试这一点。

我们来分解一下向`"FallbackAPIQueue"`发送消息的函数的关键部分。

在第 2 行，我们从环境变量中检索我们的连接 URL `AMQP_SERVER_URL`。

接下来，我们使用`amqp.Dial()`建立一个**连接**。我们还没完。使用 RabbitMQ，大多数操作都是通过通道来处理的。我们可以在我们的连接上使用`Channel()`检索一个**通道**(在第 8 行)

最后，我们可以推迟**连接**和**通道**上的`Close()`功能，以确保在我们将消息提交到队列后连接被正确关闭。

在第 13 行，我们声明了要向其提交消息的队列。为此，我们使用 AMQP 的`[func (*Channel)[QueueDeclare]](https://github.com/streadway/amqp/blob/v1.0.0/channel.go#L751)`函数。

```
_, err = channelRabbitMQ.QueueDeclare(
		"FallbackAPIQueue",
		true,           // durable
		false,          // auto delete
		false,          // exclusive(only declaring channel can access the queue)
		false,          // no wait
		nil,            // table
	) 
```

如果尚未创建队列，此函数将创建具有给定名称的队列。

在第 24 行，我们使用`amqp.Publishing`定义我们的消息。我们将使用一个简单的纯文本消息。

```
message := amqp.Publishing{
		ContentType: "text/plain",
		Body:        []byte("A request has been sent via fallback API"),
	}
```

最后，我们可以在我们的**频道**上发布我们的消息`Publish`。

```
channelRabbitMQ.Publish(
		"",                      // exchange
		"FallbackAPIQueue",      // Queue
		false,                   // mandatory
		false,                   // immediate
		message,
);
```

当该函数被触发时，消息将被排队到我们的`"FallbackAPIQueue"`中，我们可以通过使用相同的命令来查看队列和消息数量:

```
➜  ~ sudo rabbitmqctl list_queues
Password:
Timeout: 60.0 seconds ...
Listing queues for vhost / ...
name	messages
FallbackAPIQueue	3
```

至此，我们完成了消息发布机制🚀

在下一章中，我们将研究如何使用`gocron`包每天消费这些消息。

# 资源

*   [数字海洋博客:初始服务器设置](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-20-04)
*   [如何安装和管理 RabbitMQ](https://www.digitalocean.com/community/tutorials/how-to-install-and-manage-rabbitmq)
*   [创建 RabbitMQ 用户并提供权限](https://stackoverflow.com/questions/40436425/how-do-i-create-or-add-a-user-to-rabbitmq)
*   [RabbitMQ 安装](https://www.rabbitmq.com/install-debian.html)
*   [走](https://pkg.go.dev/github.com/streadway/amqp) `[amqp](https://pkg.go.dev/github.com/streadway/amqp)` [包](https://pkg.go.dev/github.com/streadway/amqp)