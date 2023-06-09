# 带消息传递的异步 REST 服务

> 原文：<https://medium.com/codex/async-rest-services-with-messaging-eaf4fc51a2e4?source=collection_archive---------16----------------------->

## 为什么我们需要一个信息系统？

![](img/78e19103ce58d00128c260ed38051b75.png)

[梁杰森](https://unsplash.com/@ninjason?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

大多数时候，我们使用异步 REST 来创建微服务。但是 HTTP，一种同步协议，是我们在这种情况下使用的。所以，当我们使用微服务时，REST 就变成了同步。我们采用消息方法来解决这个问题。

兔子 q

> 使用 que 管理协议
> 智能代理路由，代理中基于内容的路由

卡夫卡

> 类似于 RabbitMQ，但被认为是流处理系统
> 哑代理

对于常规的队列管理，当我们向队列提交一些东西，获取最先到达的项目，处理它，提供一个输出，然后获取下一个项目时，RabbitMQ 是理想的。

当消息到达 Kafka 使用的队列时，它会将消息复制到集合中的每个队列，并可以定向到特定的队列。我们根据一些消息属性从代理中选择队列。

如果有什么事情发生，我们可能会应用卡夫卡来通知别人。

当我们的处理能力为每分钟 10 条消息，并且每条消息的消费者处理速度为 15 秒时，我们的队列中有更多的数据。

rabbit MQ——将这些信息循环传播给许多客户。通过扩大消费者的数量，我们可以加速这一过程。如果我们的某些信息比其他信息需要更长的处理时间，那么低消费群体的信息需要更少的时间。因为它必须在处理下一条消息之前等待一条消息被处理，所以上层消费者形成一个队列。RabbitMQ 将继续发送消息并建立队列，即使在您的进程完成之后。因此，在这种情况下，较少的消费者变得闲置。当 prefetch 设置为 2 时，RabbitMQ 的公平调度特性(使用预取)将延迟发送消息，直到处理完前两个消息。

如果消费者收到一条消息，但不能立即处理，那么这条消息就不在队列中，也不在任何地方。为了避免使用 ACK，RabbitMQ 告知何时删除消息，并通知对方他们可以删除消息，因为消息已经处理完毕。

Kafka——Kafka 有一个消费群体，当考虑到与前面相同的场景时。在一个主题中，我们可以把它分成几个部分。每当我们发布关于这个话题的消息。我们从一个键开始，乘以分区的数量，然后检查哪个分区出现。如果我们使用我们的客户号作为密钥，我们可以确保相同客户的消息被发送到相同的分区。这个顺序保证是在分部而不是主体。为了确保每个分区都有自己的消费者，我们可以使用一个消费者，并指示它监听分区。这就是所谓的配置消费者组。否，客户数量应少于或等于分区数量。如果我们从某一点提供一个信息，卡夫卡有一个消费者补偿，知道从那一点开始给出下面的信息。

当需要 RabbitMQ 时，我们可以使用 Kafka。

在卡夫卡中，通常有一个主题和几个部分。

这个设置中的每个用户都在监听每个分区，但是如果我们添加更多的用户，那么这些额外的用户将会处于空闲状态。根据规定，使用者组中的使用者数量必须等于或少于主题中的分区数量。如果我们移除一个活动的消费者，额外的分区开始监听该分区。

在卡夫卡中，分区之间的顺序没有保证。

如果我们向生产者提供密钥，它将应用 mod 算法来决定根据密钥做什么。同一个密钥的使用确保了所有文件都进入同一个分区。如果没有提供密钥，Kafka 使用的循环法是不可取的，因为它不能确保订单将在同一客户和不同消息中得到履行。为了保证顺序，我们必须定义键，但是在分区内没有顺序保证。