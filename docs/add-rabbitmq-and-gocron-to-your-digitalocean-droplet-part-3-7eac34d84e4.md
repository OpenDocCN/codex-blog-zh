# 将 RabbitMQ 和 gocron 添加到您的数字海洋液滴中[第 3 部分]

> 原文：<https://medium.com/codex/add-rabbitmq-and-gocron-to-your-digitalocean-droplet-part-3-7eac34d84e4?source=collection_archive---------13----------------------->

## 用 gocron 构建一个简单的消息消费者

(这是 3 部分系列的最后一部分。点击可以阅读上一部分

![](img/b38bfe371912d283222e43583edd0277.png)

照片由 [Arwin Neil Baichoo](https://unsplash.com/@arwinneil?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 前一章的总结

在前一章中，我们已经构建了我们的消息生产者函数`enqueue`。当我们的应用服务器接收流量并向 RabbitMQ 发布消息时，就会调用`enqueue`函数。根据你的配置，你可以在其他地方调用`enqueue()`函数。

# 构建消费者

接下来，我们将构建 cron-job，使其每小时/每分钟运行一次(取决于您的偏好),并使消息从队列中出队。为了简单起见，当一条消息被消费时，我们将使用 Mailgun 的 go 包发送一封基本的通知电子邮件。

(你可以从[这里](https://github.com/asungur/simple_consumer/blob/main/main.go)找到简单消费者的回购)

在本例中，worker 将在后台运行，每 2 分钟执行一次`processMessages`功能。稍后我们将讨论如何设置这个 worker 作为一个`systemd`服务来运行。

类似于前面的例子，我们创建了一个`Connection`和一个`Channel`来执行队列中的任务。

```
amqpServerURL := os.Getenv("AMQP_SERVER_URL")
	**connectRabbitMQ, err := amqp.Dial(amqpServerURL)**
	if err != nil {
		panic(err)
	}
	defer connectRabbitMQ.Close()
	**channelRabbitMQ, err := connectRabbitMQ.Channel()**
	if err != nil {
		panic(err)
	}
	defer channelRabbitMQ.Close()
```

我们将在通道上使用`Consume()`函数来检索消息。您可以查看选项的注释。为了简单起见，我们会在消费消息时自动确认。

```
messages, err := **channelRabbitMQ.Consume**(
 **"FallbackAPIQueue",**
		"",
		true,                      // auto acknowledge
		false,                     // exclusive
		false,                     // no local
		false,                     // no wait
		nil,
	)
	if err != nil {
		log.Println(err)
	}
	log.Println("You've connected to RabbitMQ")
	log.Println("Waiting for messages")

	if len(messages) == 0 {
		log.Println("You don't have any messages in the queue")
	}
	for message := range messages {
		if string(message.Body) == "A request has been sent via fallback API" {
			sendSimpleMessage()
		}
	}
```

最后，对于我们消费的每条消息，我们将调用`sendSimpleMessage()`

使用 Mailgun 的 go 客户端库`mailgun-go`我们可以发送简单的电子邮件。这需要你有一个 Mailgun 的账户。您需要将您的 Mailgun 域和 API 密钥保存在您的环境变量中。如果您没有 Mailgun 帐户，下面的参考资料部分有一个链接。

```
func **sendSimpleMessage()** {
	api_key := os.Getenv("MAILGUN_API_KEY")
	sandbox_domain := os.Getenv("MAILGUN_DOMAIN")

	mg := mailgun.NewMailgun(sandbox_domain, api_key)
	sender := "testingsomething@example.com"
	subject := "security warning"
	body := "Your fallback api is exposed"
	recipient := "<YOUR_EMAIL>"
	message := mg.NewMessage(sender, subject, body, recipient)
	ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
	defer cancel()
	resp, id, err := mg.Send(ctx, message)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Printf("ID: %s Resp: %s\\n", id, resp)
}
```

现在，让我们配置`systemd`来运行这个应用程序作为服务。

`systemd`是大多数 Linux 发行版附带的初始化系统和系统管理器。它带有管理工具`systemctl`

如果您想了解更多关于`systemd`的知识，或者您是新手，您可以在参考资料中找到下面的教程链接。让我们回到创建我们的服务。

首先，我们将创建一个服务文件:

```
$ sudo vim /lib/systemd/system/simple_consumer.service
```

接下来，我们将在我们的`simple_consumer.service`中添加以下配置选项:

在我们继续之前，让我们讨论一下其中的一些配置步骤:

1.  在第 3 行中，`ConditionPathExists`需要确保我们的应用程序目录在开始运行之前已经存在于我们的服务器中
2.  第 6 行的`After`告诉`systemd`等待，直到`postgresql`服务开始。这是必需的，因为我要将它集成到我的简单 API 服务器中。根据您的配置，这可能不是必需的。
3.  在第 12 行，我们指定了`WorkingDirectory`。这对于从我们的`.env`文件中检索我们的环境变量很重要。
4.  最后，`ExecStart`指定我们的可执行文件的位置

在启用我们的系统之前，我们需要给予相关的权限来运行我们的应用程序:

```
$ sudo chmod +x /opt/appDir/simple_consumer
```

现在使用`systemctl`我们可以首先启用服务并启动它。我们可以使用最后一个命令来检查应用程序的状态:

```
$ sudo systemctl enable simple_consumer      # Enable the service
$ sudo systemctl start simple_consumer       # Start the service
$ sudo systemctl status simple_consumer      # Check service status
```

现在，让 trigger 排队几次，并在 cron-job 运行前后查看我们的队列:

```
$ sudo rabbitmqctl list_queuesTimeout: 60.0 seconds ...
Listing queues for vhost / ...
name messages
FallbackAPIQueue 3
```

`FallbackAPIQueue`旁边的数字应该下降到`0`，在我们的收件箱中，我们应该有一封电子邮件，用于发送从队列中出列的每条消息🎊

# 资源

*   [如何使用 Systemctl 管理 Systemd 服务和单元](https://www.digitalocean.com/community/tutorials/how-to-use-systemctl-to-manage-systemd-services-and-units)
*   `[gocron](https://pkg.go.dev/github.com/go-co-op/gocron)`包[包](https://pkg.go.dev/github.com/go-co-op/gocron)
*   [在 Ubuntu 上运行 Go app 作为服务](https://www.atpeaz.com/running-go-app-as-a-service-on-ubuntu/)
*   [Go mailgun 包](https://pkg.go.dev/github.com/BoltApp/mailgun-go)
*   [配置你的 Mailgun 账户](https://documentation.mailgun.com/en/latest/quickstart-sending.html)