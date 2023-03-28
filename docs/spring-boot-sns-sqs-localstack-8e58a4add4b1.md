# Spring Boot +社交网络+ SQS +本地堆栈

> 原文：<https://medium.com/codex/spring-boot-sns-sqs-localstack-8e58a4add4b1?source=collection_archive---------3----------------------->

在[之前的](https://turkdogan.medium.com/spring-boot-sns-localstack-619b9b75f2ac)帖子中，我们建立了一个 Spring Boot 项目，并实现了几个亚马逊简单通知服务(SNS)任务。在本帖中，我们将继续集成亚马逊简单队列服务(SQS)。首先，我们将开发一个客户端来连接亚马逊 SQS。然后，我们将使用这个客户端创建队列。最后一步，我们将订阅 Amazon SNS，将特定消息重定向到相应的队列。

注意:你可以从[这里](https://github.com/turkdogan/spring-boot-guide/tree/main/spring-boot-sns-sqs-localstack)下载代码作为 Kotlin+Gradle 项目

# 运行 LocalStack

我们需要在 docker-compose 文件中添加 Amazon SNS 和 SQS。

```
@Configuration
class AWSSQSConfig { @Bean(destroyMethod = "shutdown")
  fun amazonSQS(): AmazonSQSAsync {
    return AmazonSQSAsyncClient.asyncBuilder()
         .withEndpointConfiguration(AwsClientBuilder.EndpointConfiguration(
                    "http://localhost:4566", "us-east-1"))
            .withCredentials(AWSStaticCredentialsProvider(
                    BasicAWSCredentials("foo", "bar")))
            .build()
  }
```

让我们启动 LocalStack 服务。

```
docker-compose up -d
```

从现在开始，我们可以通过使用 LocalStack 来测试我们的服务。

# SQS 客户

我们需要亚马逊提供的 SQS 图书馆。我们可以连接到亚马逊 SQS 和执行 SQS 相关的操作。让我们创建一个 Spring SQS 客户端组件。

```
@Configuration
class AWSSNSConfig { @Bean(destroyMethod = "shutdown")
    fun amazonSNS(): AmazonSNS {
        return AmazonSNSClient.builder()
                .withEndpointConfiguration(AwsClientBuilder.EndpointConfiguration(
                        "http://localhost:4566", "us-east-1"))
                .withCredentials(AWSStaticCredentialsProvider(
                        BasicAWSCredentials("foo", "bar")))
                .build()
    }
}
```

LocalStack 使用 4566 端口与所有 AWS 服务进行通信。我们不需要提供任何真实的凭证来与 LocalStack 通信。

# 常见的 SQS 场景

让我们在 Spring Boot 写几个 SQS 测试:

```
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
@TestMethodOrder(MethodOrderer.OrderAnnotation::class)
class TestSQS() { private val queue = UUID.randomUUID().toString() @Autowired
    private lateinit var amazonSQS: AmazonSQSAsync private lateinit var queueUrl: String private lateinit var message: Message @Test
    @Order(1)
    fun testCreateQueue() {
        val result = amazonSQS.createQueue(queue)
        queueUrl = result.queueUrl
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
    } @Test
    @Order(1)
    fun testCreateFifoQueue() {
        val request = CreateQueueRequest()
        request.queueName = "$queue.fifo"
        request.addAttributesEntry("FifoQueue", "true")
        val result = amazonSQS.createQueue(request)
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
    } @Test
    @Order(2)
    fun testListQueues() {
        val result = amazonSQS.listQueues()
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
        Assertions.assertTrue(result.queueUrls.isNotEmpty())
        Assertions.assertTrue(result.queueUrls.contains(queueUrl))
    } @Test
    @Order(3)
    fun testSendMessage() {
        val request = SendMessageRequest()
        request.messageBody = "This is SQS message"
        request.queueUrl = queueUrl
        val result = amazonSQS.sendMessage(request)
        val messageId = result.messageId
        Assertions.assertNotNull(messageId)
        val receiveMessageResult = amazonSQS.receiveMessage(queueUrl)
        message = receiveMessageResult.messages.first()
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
        Assertions.assertEquals(request.messageBody, message.body)
        Assertions.assertEquals(messageId, message.messageId)
    } @Test
    @Order(4)
    fun testDeleteMessage() {
        val request = DeleteMessageRequest()
        request.queueUrl = queueUrl
        request.receiptHandle = message.receiptHandle
        val result = amazonSQS.deleteMessage(request)
        val receiveMessageResult = amazonSQS.receiveMessage(queueUrl)
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
        Assertions.assertTrue(receiveMessageResult.messages.isNullOrEmpty())
    } @Test
    @Order(5)
    fun testDeleteQueue() {
        val result = amazonSQS.deleteQueue(queueUrl)
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
    }
}
```

请注意，我们按照特定的顺序运行测试。在第一个测试中，我们创建了一个 SQS 队列。在第二个测试中，我们检索在亚马逊 SQS 中创建的队列。我们应该会看到在第一个测试中创建的队列。然后，我们将消息发送到队列，并从队列中读取该消息。在最后两个测试场景中，我们删除了队列消息和队列。

# SQS 和社交网站的整合

在这一部分，我们将整合 SNS 和 SQS。每当我们发送 SNS 消息时，订阅的队列应该会收到该消息。整个测试文件如下所示:

```
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
@TestMethodOrder(MethodOrderer.OrderAnnotation::class)
class TestSQSIntegration { private val topic = "topic" private val queue1 = UUID.randomUUID().toString() private val queue2 = UUID.randomUUID().toString() @Autowired
    private lateinit var amazonSNS: AmazonSNS @Autowired
    private lateinit var amazonSQS: AmazonSQSAsync private lateinit var topicArn : String private lateinit var queueUrl1: String
    private lateinit var queueUrl2: String @Test
    @Order(1)
    fun testCreateTopic() {
        val createTopic = amazonSNS.createTopic(topic)
        topicArn = createTopic.topicArn
        Assertions.assertEquals(200, createTopic.sdkHttpMetadata.httpStatusCode)
    } @Test
    @Order(2)
    fun testCreateQueues() {
        var result = amazonSQS.createQueue(queue1)
        queueUrl1 = result.queueUrl
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode) result = amazonSQS.createQueue(queue2)
        queueUrl2 = result.queueUrl
        Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
    } @Test
    @Order(3)
    fun testSubscriptions() {
        // first queue
        var subscribeQueue = Topics.subscribeQueue(amazonSNS, amazonSQS, topicArn, queueUrl1)
        Assertions.assertTrue(subscribeQueue.contains(topic)) // second queue
        subscribeQueue = Topics.subscribeQueue(amazonSNS, amazonSQS, topicArn, queueUrl2)
        Assertions.assertTrue(subscribeQueue.contains(topic))
    } @Test
    @Order(4)
    fun testPublish() {
        val request = PublishRequest()
        request.topicArn = topicArn
        request.subject = "This is a sample subject"
        request.message = "This foo is a sample message"
        request.messageGroupId = "ExampleGroupId"
        val result = amazonSNS.publish(request) val receiveMessageResult1 = amazonSQS.receiveMessage(
                ReceiveMessageRequest()
                        .withWaitTimeSeconds(5)
                        .withQueueUrl(queueUrl1)
        ) val receiveMessageResult2 = amazonSQS.receiveMessage(
                ReceiveMessageRequest()
                        .withWaitTimeSeconds(5)
                        .withQueueUrl(queueUrl2)
        ) val objectMapper = ObjectMapper() val message1 = receiveMessageResult1.messages.first()
        val bodyMap1 = objectMapper.readValue(message1.body, Map::class.java) val message2 = receiveMessageResult2.messages.first()
        val bodyMap2 = objectMapper.readValue(message2.body, Map::class.java) Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
        Assertions.assertNotNull(result.messageId) Assertions.assertTrue(receiveMessageResult1.messages.isNotEmpty())
        Assertions.assertEquals(request.message, bodyMap1["Message"])
        Assertions.assertEquals(topicArn, bodyMap1["TopicArn"])
        Assertions.assertEquals(request.subject, bodyMap1["Subject"]) Assertions.assertTrue(receiveMessageResult2.messages.isNotEmpty())
        Assertions.assertEquals(request.message, bodyMap2["Message"])
        Assertions.assertEquals(topicArn, bodyMap2["TopicArn"])
        Assertions.assertEquals(request.subject, bodyMap2["Subject"])
    }
}
```

让我们一个功能一个功能地解释这段代码。与第一个测试文件一样，这个文件中的测试也按照指定的顺序工作。在第一个测试中，我们创建了一个 SNS 主题。然后在第二个例子中，我们创建了两个亚马逊 SQS 定义。在第三个测试中，我们订阅了 Amazon SNS 主题的队列。

在最后一个测试场景中，我们发送一条 SNS 消息，并期望两个队列都接收到该消息。消息内容以 JSON 格式发送。下面是一条典型的 JSON 格式的 SQS 消息:

```
{
   "MessageId":"4e68039b-faaf-4aba-67da-fe75cfcb0b4b",
   "ReceiptHandle":"xxwcwycayeifjskwiqribwcxsaxjbbsoqtwidvunpekzjhxwppszbufvvecxoyaexylajcpmyrdibhlqdjdoyfjeqihvuwkrdjhermstvyblisrhpswpznglwhcesbnskcyxymvonfyzjtykkoikyasnksafaegwsvdlkaiptjmrihzuduyzprdmg",
   "MD5OfBody":"2cb3e645c71bf662f72fe8ca5b5c5d12",
   "Body":{
      "Type":"Notification",
      "MessageId":"9774de9f-1677-4ac9-a35d-95cce390ab5a",
      "TopicArn":"arn:aws:sns:us-east-1:000000000000:topic",
      "Message":"This foo is a sample message",
      "Timestamp":"2021-07-22T09:04:00.495Z",
      "SignatureVersion":"1",
      "Signature":"EXAMPLEpH+..",
      "SigningCertURL":"https://sns.us-east-1.amazonaws.com/SimpleNotificationService-0000000000000000000000.pem",
      "Subject":"This is a sample subject"
   },
   "Attributes":{

   },
   "MessageAttributes":{

   }
}
```

在测试的最后一部分，我们将 JSON 值转换成一个映射，以便能够检索消息的细节。然后，我们验证了队列检索的消息具有正确的内容。

# 社交网络信息过滤

到目前为止，当我们发送 SNS 消息时，所有订阅的服务都会检索该消息。有可能只将消息重定向到相关的接收者，而不阻塞不相关的队列。Amazon SNS 提供消息过滤支持，以区分消息的接收者。过滤器策略是一个 JSON 对象，用于将特定的策略内容映射到相应的队列。

# 将过滤策略添加到社交网络

添加筛选器策略需要有效的订阅 ARN。

```
var subscriptionArn = Topics.subscribeQueue(amazonSNS, amazonSQS, topicArn, queueUrl1)
Assertions.assertTrue(subscriptionArn.contains(topic))var filterPolicyString = "{\"event\":[\"${filterPolicy1}\"]}"
var request = SetSubscriptionAttributesRequest(subscriptionArn, "FilterPolicy", filterPolicyString)
amazonSNS.setSubscriptionAttributes(request)
```

请注意，我们必须使用`FilterPolicy`属性名称来提供策略内容。

# 发送带有策略的 SNS 消息

为了能够添加策略消息，我们必须提供策略内容作为消息属性。在下面的例子中，过滤器策略包含一个带有值的`event`键属性。该值必须与我们在主题的队列订阅过程中提供的值相匹配。

```
@Test
@Order(4)
fun testRedirectToFirstQueueOnly() {
    val request = PublishRequest()
    request.topicArn = topicArn
    request.subject = "This is a sample subject"
    request.message = "This foo is a sample message"
    request.messageGroupId = "ExampleGroupId" val messageAttributeValue = MessageAttributeValue().withDataType("String.Array")
            .withStringValue("[\"$filterPolicy1\"]")
    request.addMessageAttributesEntry("event", messageAttributeValue) val result = amazonSNS.publish(request) val receiveMessageResult1 = amazonSQS.receiveMessage(
            ReceiveMessageRequest()
                    .withWaitTimeSeconds(5)
                    .withQueueUrl(queueUrl1)
    ) val receiveMessageResult2 = amazonSQS.receiveMessage(
            ReceiveMessageRequest()
                    .withWaitTimeSeconds(5)
                    .withQueueUrl(queueUrl2)
    ) val objectMapper = ObjectMapper() val message1 = receiveMessageResult1.messages.first()
    val bodyMap1 = objectMapper.readValue(message1.body, Map::class.java) Assertions.assertEquals(200, result.sdkHttpMetadata.httpStatusCode)
    Assertions.assertNotNull(result.messageId) Assertions.assertTrue(receiveMessageResult1.messages.isNotEmpty())
    Assertions.assertEquals(request.message, bodyMap1["Message"])
    Assertions.assertEquals(topicArn, bodyMap1["TopicArn"])
    Assertions.assertEquals(request.subject, bodyMap1["Subject"]) Assertions.assertTrue(receiveMessageResult2.messages.isEmpty())
}
```

当我们运行这个测试场景时，我们应该观察到只有第一个队列必须检索消息。

# 摘要

在这篇文章中，我们实现了一些亚马逊 SQS 的功能，比如创建队列、发送消息。此外，我们集成了亚马逊 SQS 和 SNS，并将 SNS 消息重定向到相应的队列。多亏了 LocalStack，我们在没有连接到 Amazon 云的情况下离线测试了所有实现。