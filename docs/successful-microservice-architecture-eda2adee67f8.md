# 成功的微服务架构

> 原文：<https://medium.com/codex/successful-microservice-architecture-eda2adee67f8?source=collection_archive---------5----------------------->

## 如何在设计涉及同步调用的实时系统时取得成功？

![](img/32a922aba50fe085b0e36d36e2b6e0af.png)

[src](https://memegenerator.net/instance/68203746/lemon-meme-when-life-gives-you-lemons-make-lemonade)

同步调用是您应该避免的。它们降低了可用性，增加了延迟，并有其他缺点。AWS 有一篇 [builder's library](https://aws.amazon.com/builders-library/challenges-with-distributed-systems/) 文章，其中描述了操作同步系统的难度:

> 在最困难的一端，我们有硬实时分布式系统。这些通常被称为请求/回复服务。(*重点地雷*)

然而有时，有些系统必须进行同步调用。本文为服务提供商和客户提供了在这种情况下如何取得成功的技巧。

# 作为服务提供商

## 故障设计

*   设计[等幂服务](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/?did=ba_card&trk=ba_card)——让客户端可以安全地“盲目重试”错误；如果需要，传递一个唯一的令牌
*   使用[无状态计算](https://cloudonaut.io/a-pattern-for-continuously-deployed-immutable-and-stateful-applications-on-aws/)，这样当一个实例失败或流量增加时，您可以[水平扩展](https://www.section.io/blog/scaling-horizontally-vs-vertically/)

## 认识到服务错误会级联

*   [将您的发布从您的部署中分离出来](https://www.split.io/blog/why-would-i-want-to-decouple-deployment-from-release/)并在路由流量之前执行验证( [sam 流量挂钩](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-property-function-hooks.html) / [Kubernetes 准备就绪检查](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
*   对您的变更使用[canary deployments](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/automating-updates-to-serverless-apps.html)——这可以让您只对一小部分请求进行变更，从而最小化坏变更的影响
*   确保你的服务是[向后兼容的](https://aws.amazon.com/builders-library/ensuring-rollback-safety-during-deployments/)——这保证你可以在引入错误时**安全地回滚**
*   监控您的服务—知道何时回滚。利用[现成的 cloudwatch 指标](https://docs.aws.amazon.com/lambda/latest/dg/monitoring-metrics.html)，但是要意识到你可能需要发布[自定义指标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html)，很可能使用 [EMF](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format_Specification.html) (比如 2xx 延迟)
*   [警报时自动回滚](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/automating-updates-to-serverless-apps.html) —有了 AWS sam，您无需再次部署，从而最大限度地缩短了补救时间
*   **使用您提供给客户的相同(生成的)SDK 来测试您的(部署的)服务**

## 让您客户的生活变得轻松

*   提供一个 SDK/库(您在功能测试中审查的),消费者可以将它包含在他们的项目中，并用来调用您的服务
*   将您的端点发布到服务发现([参数存储库](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)或 [eureka](https://spring.io/guides/gs/service-registration-and-discovery/) )并解析 url，然后*在您的测试中使用它*

## 确定请求的优先级

*   如果你有健康检查( [Kubernetes 应用](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)，或者[elb 后面的应用](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/elb-healthchecks.html))，优先考虑它们
*   拒绝过期请求—检查原始请求超时的传播过期标头。如果超过超时，就不要处理。

## 使调试变得容易

*   始终使用一个[关联 id](https://www.rapid7.com/blog/post/2016/12/23/the-value-of-correlation-ids/)——如果传递了一个，就使用它，如果没有，就生成自己的 id。添加为 x 射线中的[注释](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-java-segment.html)
*   回传一个请求
*   将提交散列作为一个 [x 射线注释](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-java-segment.html)和一个属性添加到 EMF 日志中——允许您更容易地调试堆栈跟踪

## 分享您的服务状态

*   发布服务状态(通过状态页面)并允许客户订阅停机提醒(您已经有了 [cloudwatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) 用于回滚)

## 生成客户端

您生成的客户端应该包括以下特性(我将有另一篇文章专门讨论这一点)

*   带[指数补偿和抖动](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)的自动重试
*   在[用户代理头](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)中发送客户端和版本
*   发布成功率和延迟指标(使用 emf)—包括客户端版本和函数名称

# 作为服务消费者(客户)

## 第一次尝试后不要放弃(给自己不止一次成功的机会)

*   **使用服务生产者**提供的 SDK，它会自动重试(还有其他好处)
*   将同步调用放在它们自己的由 sqs 触发的 lambda 中——与故障/延迟增加相分离；使用*指数补偿*进行重试(除了客户端提供的那些)

## 做一个体贴的消费者

*   环绕同步调用使用[断路器模式](https://martinfowler.com/bliki/CircuitBreaker.html)如果你正在使用 lambda，我强烈建议[遵循这种模式](/@ch.gerkens/circuit-breaker-solution-for-aws-lambda-functions-5264cb59031f)，因为它完全防止 lambda 被触发，而不仅仅是发送到 dlq
*   仅发送原始请求尚未超时的请求，并传播超时标头

## 使调试更容易

*   定期调用端点——我更喜欢使用 [cloudwatch synthetics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Synthetics_Canaries.html) 的真实流量，但是如果需要，您可以执行连接性检查，而不是健康检查

## 不要完全依赖服务器端监控

*   监控您的端到端延迟，因为服务器端指标不包括网络延迟

# 结论

我们已经看到了使用同步调用时增加成功几率的各种方法。这些责任落在服务提供者和服务消费者身上，范围从使服务幂等、使用 canary 部署和生成客户端；客户端重试请求和使用断路器模式。

你还有什么其他的技巧来保证同步系统的可靠性吗？如果有，请留下反馈。

# 即将发布的文章

[从开放 api 规范生成客户端](/codex/generating-clients-models-for-restful-services-from-openapi-specification-edf211e5d761)

[定制从开放 api 规范生成的代码](/@connorbutch/enhancing-openapi-code-generation-with-custom-features-58d38e38222)

模块的跟踪提交哈希(WIP)

运行状况检查和连接检查(WIP)之间的区别

# 进一步阅读

[](https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/) [## 使用减载来避免过载

### 软件交付和运营| 400 级您希望收到新内容的通知吗？几年来，我致力于…

aws.amazon.com](https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/) [](https://aws.amazon.com/builders-library/challenges-with-distributed-systems/) [## 分布式系统的挑战

### 从我们添加第二台服务器的那一刻起，分布式系统就成了亚马逊的生活方式。当我开始在亚马逊工作时…

aws.amazon.com](https://aws.amazon.com/builders-library/challenges-with-distributed-systems/) [](/@connorbutch/stop-making-synchronous-calls-f101af15cd46) [## 停止同步呼叫！

### 为什么同步调用会破坏你的架构

medium.com](/@connorbutch/stop-making-synchronous-calls-f101af15cd46) [](/@connorbutch/stop-making-sync-calls-part-two-4a067f27beeb) [## 停止同步呼叫！(第二部分)

### 如何通过使用事件驱动架构来改进系统设计

medium.com](/@connorbutch/stop-making-sync-calls-part-two-4a067f27beeb) [](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/?did=ba_card&trk=ba_card) [## 用幂等 API 保证重试的安全性

### 在亚马逊，我们经常在我们的服务中看到这样的模式，一个复杂的操作被分解成一个控制过程…

aws.amazon.com](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/?did=ba_card&trk=ba_card) [](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/) [## 超时、重试和抖动补偿

### 每当一个服务或系统调用另一个服务或系统时，就会发生故障。这些故障可能来自多种因素…

aws.amazon.com](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/) [](/@ch.gerkens/circuit-breaker-solution-for-aws-lambda-functions-5264cb59031f) [## AWS Lambda 功能的断路器解决方案

### CloudWatch 指标和警报可用于向 AWS Lambda 功能添加断路器功能，这些功能是…

medium.com](/@ch.gerkens/circuit-breaker-solution-for-aws-lambda-functions-5264cb59031f)