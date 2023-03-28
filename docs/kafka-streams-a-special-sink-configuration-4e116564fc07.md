# 卡夫卡溪流:一种特殊的水槽配置

> 原文：<https://medium.com/codex/kafka-streams-a-special-sink-configuration-4e116564fc07?source=collection_archive---------10----------------------->

![](img/b1b5d145bac0cf5d1cef2762ad66fc51.png)

照片由[托尔加·乌尔坎](https://unsplash.com/@tolga__?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 一个(下沉)话题，多个 AVRO 类型

这不会是一个关于卡夫卡溪流的教程。网上到处都是，有些真的真的很好。这也不是关于模式注册的教程。和以前一样，谷歌一下，你可以找到所有你需要的答案。

实际上，本文描述了流处理器的一个非常具体的配置，并暗示了对 Kafka 流的良好了解，以及当您希望您的流处理器消费、处理和产生 AVRO 消息时，如何将其与融合模式注册中心集成。

我想实现的是向您展示如何创建和配置一个流处理器，它可以在接收器主题上产生不同类型的 AVRO 消息。这是一个非常具体的用例，但在某些情况下会很有用。

我们走吧。

## 简单的水槽

当您在 Kafka Streams 拓扑上配置接收器时，您通常会编写如下内容

很简单，不是吗？

*其实如果没有特殊要求，可以直接在流处理器配置中配置默认的序列化器和反序列化器，这里* [*解释*](https://docs.confluent.io/platform/current/streams/developer-guide/datatypes.html#configuring-serdes) *。在这种情况下，* `*to()*` *方法可以称为省略了* `*Produced*` *指令。*

## 不太简单的水槽

假设现在您希望您的流处理器产生 AVRO 消息，并在您的流处理器中包含模式注册支持(这有许多好处，列举它们超出了本文的范围，所以请阅读 [this](https://www.confluent.io/blog/schema-registry-kafka-stream-processing-yes-virginia-you-really-need-one/) )。为此，您必须配置特定的 AVRO 序列化程序，并在您的拓扑配置中使用它。

让我们使用一个支持类来实现这个目的。

这个类包装了关于一个主题的所有信息:名称、键`SerDe`(串行化器/去串行化器)和值`SerDe`。`configureValueSerDe()`方法允许我们通过模式注册中心的 url 来配置值`SerDe`。

*在本文中，我假设键总是一个字符串，我们只需要配置值序列化程序，但是将键配置为 AVRO 对象也很简单。*

您可以在流处理器中使用这个类来配置接收器。

有了这个拓扑，您的流处理器就可以发布 AVRO 消息了。

好了，现在有趣的事情来了！

## 一款水槽，多种型号

假设您希望您的流处理器在目的地主题上生成不同类型的消息。这是一个在许多用例中有意义的需求(参见这里的[和这里的](https://www.confluent.io/blog/put-several-event-types-kafka-topic/)和[但是在 Kafka Streams 拓扑中似乎很难实现，尤其是当您使用漂亮的 Kafka Streams DSL 时……但是真的有那么难吗？](https://www.confluent.io/blog/multiple-event-types-in-the-same-kafka-topic/)

其实挺简单的，一行代码的事情。这里是修改后的`TopicSerDe`级。

如您所见，更新后的`configureValueSerde()` 方法还将存储在模式注册中心的模式的命名策略添加到了`SerDe`配置中。有了这个配置，您的流处理器将使用`TopicRecordNameStragegy`在模式注册表中存储主题，您可以生成所有需要的消息类型，而不受 Kafka Streams 默认使用的默认模式命名策略的限制(`TopicNameStrategy`，实际上意味着“每个主题一种类型”)。

## 放弃

我并不是说设计一个解决方案，每个主题只发布一种类型的消息是错误的。在我的日常工作中，这是我使用的默认设计，因为它很清晰，也因为你可以从融合平台的其他工具(尤其是 ksqlDB，这是一个非常棒的工具)获得更好的支持。

不过，在某些情况下，最好就一个主题发布不同类型的消息。上面链接的文章清楚地解释了这个概念，但我给出我的 2 美分，让它更清楚。

让我们想想与一个电子商务网站的`Order`相关的事件。如果您将它们建模为支持 AVRO 的 Java 对象，您将拥有`OrderCreated, PaymentAccepted, OrderShipped`等等。如果您希望这些事件被下游的消费者以正确的顺序消费，那么您必须将它们发布在一个专用的主题上，仔细选择分区键以便将它们发布在同一个分区中(并且您肯定知道 Kafka 保证了消息在分区级别的顺序)。

## 回购

在这里，您可以找到使用此配置的示例

[https://github.com/darkwings/event-sourced-kstream](https://github.com/darkwings/event-sourced-kstream)

希望这篇小文章有用。下次见。