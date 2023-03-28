# AWS API Gateway+Lambda——我需要更多的咖啡

> 原文：<https://medium.com/codex/aws-api-gateway-lambda-im-going-to-need-more-coffee-for-this-21a210d48ec0?source=collection_archive---------2----------------------->

![](img/e0c2b276639ecb959f17d9eec83e20bf.png)

泰姆斯米德的屋顶海洋(作者拍摄)

如果你回顾了我在过去几个月中使用 AWS Lambda 函数的经历，你可能不会惊讶地发现我的注意力已经转向想要探索在 Lambda 函数之前栓接一个 API。在这种疯狂中有一些方法:虽然我对基于 Lambda 的处理和存档照片的管道很满意，但我真的想在上面放一个 web 界面，以便于浏览照片。我最初的想法是定期构建静态网页(因为我是按日期组织的)，但仔细想想，这是一个丑陋的想法。所以……是时候研究如何组装一个无服务器 API 了。

我首先要说的是，AWS 让这变得比它应该变得更难了。围绕这一点的文档和教程材料可能会好得多。这些材料实际上没有什么*错误*，但是它们是不透明和分散的。我认为这是由两件事造成的。首先，AWS API 网关有两个版本，每个版本都支持 HTTP 和 WebSockets，语义和配置略有不同。所以很多(零碎的)教程材料都在谈论 WebSockets 而不是 HTTP，或者谈论版本 1 而不是版本 2 产品，或者将所有这些混合在一起。其次，用于管理 AWS API 网关的公开 API 和使用控制台的体验之间存在微妙的语义差异。坦率地说，我认为控制台是设置 API 网关的一个非常好的界面，其逻辑流程很有意义。它和 AWS API 不太匹配，当然也和 Terraform 不太匹配。

现在，如果你回头看看我写的东西，你会注意到我是基础设施即代码和[地形](https://www.terraform.io)的粉丝。因此，即使对于这样一个简单的探索，我的偏好也是用 Terraform 来构建解决方案。有很多人谈论基础设施即代码对于可重复性、安全性或开发速度的好处，但对我来说还有一个额外的目的:一旦你习惯了阅读它，Terraform 代码中使用的 Hashicorp HCL 可以很好地记录基础设施是如何配置的。在控制台上单击并不提供该记录，并且原始 API 调用在应用和管理配置的所有粘合代码中掩盖了配置。

所以。让我们继续吧。我最初的实验是一个非常简单的 API。几个月前，我写了一些 Java 代码来探索如何使用 [AWS CodeCommit](https://aws.amazon.com/codecommit/) 和 [AWS CodeArtifact](https://aws.amazon.com/codeartifact/) (参见[https://level up . git connected . com/AWS-code artifact-with-maven-further-adventures-with-server less-4ff 07 FD 69 E1 b](https://levelup.gitconnected.com/aws-codeartifact-with-maven-further-adventures-with-serverless-4ff07fd69e1b))，所以对它的逻辑扩展是将这个库包装在一个 Lambda 中，并在它前面加上一个 API。

这包括三个部分:定义 API 的 Terraform 代码、定义 Lambda 的 Terraform 代码和一些定义 Lambda 的 Java 代码。我还建立了基础设施来执行 Lambda 代码的无服务器构建，但我不会在这里花时间。(如果你有兴趣，请前往:[https://ra hook . medium . com/server less-code-pipelines-on-AWS-30 DFC 91889 c 6](https://rahook.medium.com/serverless-code-pipelines-on-aws-30dfc91889c6))。我也不会在定义 Lambda 的 Terraform 代码上花太多时间，因为除了 API 网关和 Lambda 之间的一点点连接之外，我在过去已经写过了。

最好从 AWS API 网关开始。这在 AWS 文档中有所涉及，但那是一个错综复杂的段落，都是一样的。该定义有四个基本部分，不包括自定义域名的配置和某种认证/授权方案的配置。

首先，有 [API 网关](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/apigatewayv2_api)本身。这里没有太多要定义的，它主要是一个存在的陈述，并成为所有其他配置的根源:

```
resource "aws_apigatewayv2_api" "cidrapi" {
  name = local.name
  description   = "description here..."
  version       = "1.0.0"
  protocol_type = "HTTP"
  disable_execute_api_endpoint = false
  tags = merge({ "Name" = local.name }, var.tags)
}
```

API 网关获得一个名称、一个描述和一个类型——指定`disable_execute_api_endpoint`无论如何都是默认的，但是我把它留在那里是为了提醒自己，当我想在它的位置添加一个自定义域时，禁用默认端点。

接下来，您有一个或多个[集成](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/apigatewayv2_integration)来指定后端将会是什么。

```
resource "aws_apigatewayv2_integration" "cidrapi" {
  api_id           = aws_apigatewayv2_api.cidrapi.id
  integration_type = "AWS_PROXY" connection_type        = "INTERNET"
  description            = "cidrapi service"
  integration_method     = "POST"
  integration_uri        = aws_lambda_function.cidrapi.invoke_arn
  payload_format_version = "2.0"
  timeout_milliseconds   = 30000
}
```

有一个 *lot* 可以在这些集成上配置，即使是像这样一个简单的 Lambda。这大部分都很简单，但是文档中对 *integration_method* 和 *integration_type* 解释得不够清楚。对于 lambda 来说，不明显的是只支持 POST，这代表了网关调用 Lambda 的方式，与公开的 API 方法没有任何关系，这些方法可能是 POST、GET、DELETE 或任何您喜欢的方法。“代理”这个词的使用也有点误导，但是如果您认为 API Gateway 不仅仅是一个简单的门面的话，这是有意义的。

在他们的文档和例子中没有很好的表达，但是 API Gateway，尤其是新版本，是一项非常令人印象深刻和灵活的技术。各种介绍性材料和教程可能会让您认为 API Gateway 仅限于在一些后端服务前设置一个门面。相反，可以将它视为一个独立的 web 服务器(有点像 [NGINX](https://www.nginx.com) )，它可以直接响应传入的请求，将请求传递给后端服务或外部 URL，管理身份验证和 TLS 终止，并进行复杂的流量过滤、转换和整形。

在我们简单的 Lambda 案例中，我们实际上只是使用 API Gateway 作为代理，以 Lambda 可以消化的形式(通过 POST)将传入的请求传递给 Lambda，并向调用者进行反向转换。对于大多数 API+Lambda 场景来说，这将是一个非常(非常)通用的模型，他们构建的架构在公开的 API 和 Lambda 后端代码之间提供了非常好的解耦。

我们有一个 API，我们有一个或多个集成—下一步是定义 [*路线*](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/apigatewayv2_route) *。*一般来说，这些定义了 API 将响应什么操作，以及如何处理这些操作。同样，在定义授权方面有很大的灵活性，并且有一些转换请求参数的工具，但是对于我们的简单例子，我们可以只传递请求:

```
resource "aws_apigatewayv2_route" "regions" {
  operation_name = "listRegions"
  api_id         = aws_apigatewayv2_api.cidrapi.id
  route_key      = "GET /regions"
  target         =
     "integrations/${aws_apigatewayv2_integration.cidrapi.id}"
}resource "aws_apigatewayv2_route" "services" {
  operation_name = "listServices"
  api_id         = aws_apigatewayv2_api.cidrapi.id
  route_key      = "GET /services"
  target         =
     "integrations/${aws_apigatewayv2_integration.cidrapi.id}"
}resource "aws_apigatewayv2_route" "cidr" {
  operation_name = "listCidr"
  api_id         = aws_apigatewayv2_api.cidrapi.id
  route_key      = "GET /cidr"
  target         =
     "integrations/${aws_apigatewayv2_integration.cidrapi.id}"
}resource "aws_apigatewayv2_route" "cidr_region" {
  operation_name = "listCidrByRegion"
  api_id         = aws_apigatewayv2_api.cidrapi.id
  route_key      = "GET /cidr/{region}"
  target         =
     "integrations/${aws_apigatewayv2_integration.cidrapi.id}"
}resource "aws_apigatewayv2_route" "cidr_service" {
  operation_name = "listCidrByService"
  api_id         = aws_apigatewayv2_api.cidrapi.id
  route_key      = "GET /cidr/{region}/{service}"
  target         =
     "integrations/${aws_apigatewayv2_integration.cidrapi.id}"
}
```

在我的例子中，我定义了 5 个操作，并将它们交给我的集成来处理。这里需要注意一些事情。我可以有一个使用`$default`作为`route_key`的单一路由，但是这可能会要求我的 Lambda 需要更多的逻辑来理解传入的请求。此外，通过使用特定的`route_key`，API 网关本身负责拒绝我不感兴趣的传入请求，而不是强制 Lambda 调用拒绝这些请求——换句话说，随意调用暴露的 API 不会导致不必要的 Lambda 调用。最后，您可能已经注意到，每条路线都可能有不同的目标，这对于一个重要的应用程序来说是非常可能的。

关于[路由关键字](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-develop-routes.html)的文档非常混乱，不清楚是否可以定义预期的路径变量(例如上面例子中的`{region}`)以及“catch all”变量，这些变量或多或少代表 URL 末尾的通配符匹配(他们在文档中使用`{proxy+}`，只是为了增加混乱)。这些路径变量将显示为 Lambda 代码可用的路径变量的有序列表。如果你用了一个路线键，比如

```
GET /{proxy+}
```

然后*任何*请求路径都会被接受，路径的所有元素最终都会在 Lambda 处理程序中显示为路径元素的有序列表。这看起来很方便，但是它使得 Lambda 代码更加复杂。在 API Gateway 中定义相当精确的路线，让您的 Lambda 代码更简单、更笨。

到目前为止，我们有 API 网关，一个或多个集成，一些路线。最后缺失的部分是相当混乱的命名“[阶段](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-stages.html)”。我怀疑 AWS 中的设计和意图在实现过程中发生了部分改变，文档非常简洁，没有提供任何信息。*可能*他们认为这是一种公开不同版本的 API，或者公开开发/测试/生产版本的方式(请*不要这样做*——在这些阶段使用不同的帐户！)但是这里允许直接指定 Lambdas(绕过集成配置)，并在请求中插入额外的参数(绕过路由配置)，这就变得混乱了。最后，您可以使用神奇的名称`$default`创建一个默认的 stage，但是您可能不应该这样做。我需要花更多的时间在服务的这个特别混乱的角落。

尽管围绕着*阶段*有些混乱，但是你不能不使用它——没有一个定义好的阶段，API 就不会被发布并可供调用。另外，在*阶段*中，您可以选择指定日志记录、监控和(特别是)速率限制:

```
resource "aws_apigatewayv2_stage" "v1" {
  api_id      = aws_apigatewayv2_api.cidrapi.id
  name        = "v1"
  description = "V1 for ${local.name}"
  auto_deploy = true default_route_settings {
    throttling_burst_limit = 50
    throttling_rate_limit  = 100
  }
  tags = merge({ "Name" = local.name, "Version" = "V1" }, var.tags)
}
```

由于这个 API 将会出现在互联网上(尽管有一个模糊的随机 URL)，我想严格限制我的 Lambda 被调用的频率。这个月我最不需要的就是 AWS 的意外账单！

您还会注意到我将`auto_deploy`设置为`true`——如果我将其设置为 false，那么我还必须指定一个[部署](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/apigatewayv2_deployment)。虽然这样做的目的是让我能够控制 API 的发布时间，但这不是我现在需要的。API 一经定义就发布，并且每当它的定义改变时就更新。

毕竟，这意味着我们可以使用 stage 的`invoke_url`属性来调用 Lambda。你应该记得我有一条路线，路线关键字是这样的

```
GET /cidr/{region}/{service}
```

我用`name`“v1”定义了这个阶段。我可以调用的结果 URL 变成了:

```
https://mqciw5p4x8.execute-api.eu-west-2.amazonaws.com/v1/cidr/eu-west-2/EBS
```

(那可能对你有用，但是我不能保证 URL 没有改变！)

最后一点——我们需要允许 AWS API Gateway 调用我们的 Lambda。我总是被这个绊倒，可能是因为 Lambda 权限不是通过 AWS IAM 服务管理的。所以最后一部分是:

```
resource "aws_lambda_permission" "allow_api" {
  for_each = toset([
    "services",
    "cidr",
    "cidr/{region}",
    "cidr/{region}/{service}",
    "regions"
  ]) statement_id_prefix = "ExecuteByAPI"
  action              = "lambda:InvokeFunction"
  function_name       = aws_lambda_function.cidrapi.function_name
  principal           = "apigateway.amazonaws.com"
  source_arn          = "${aws_apigatewayv2_api.cidrapi.execution_arn}/*/*/${each.key}"
}
```

这并不是非常复杂，尽管相对于`execution_arn`指定`source_arn`的需求有点笨拙。在这种情况下，我为每个潜在的输入源创建独立的权限，但是我可以用通配符代替`${each.key}`创建一个单独的权限，例如

```
resource "aws_lambda_permission" "allow_api" {
  statement_id_prefix = "ExecuteByAPI"
  action              = "lambda:InvokeFunction"
  function_name       = aws_lambda_function.cidrapi.function_name
  principal           = "apigateway.amazonaws.com"
  source_arn          = "${aws_apigatewayv2_api.cidrapi.execution_arn}/*/*/*"
}
```

然而，我选择不这样做，因为我更喜欢从一开始就应用最小特权的原则，而不是在我可能忘记的时候试图添加它。如果你的 Lambda 支持大量的请求 URL，这种方法可能不适合你，但是如果你的 Lambda 正在做几个不相关的任务，你应该把它分开。

在地形方面就是这样，让我们考虑 Lambda 代码。使用 [Lambda SDK](https://docs.aws.amazon.com/lambda/latest/dg/lambda-java.html) 非常愉快，调用处理程序的定义非常简单。

不过我要说的是，我并不完全喜欢为 AWS Lambda 使用 Java。当 Lambda 实例还没有预热时，API 明显很慢，当实例启动时，它需要相当长的时间来获得响应，而 Java 完成它的启动:

```
$ time curl https://mqciw5p4x8.execute-api.eu-west-2.amazonaws.com/v1/cidr/eu-west-2/EBS["18.168.37.136/29","18.168.37.144/30"]curl   0.02s user 0.01s system 0% cpu 26.383 total
```

一旦实例运行，响应时间是没问题的，但是第一次命中是致命的。在我用 Go 或者 Python 做的任何实验中，这一点都不明显。

抛开这一点，处理程序代码很简单。有一些你需要的导入:

```
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.RequestHandler;
import com.amazonaws.services.lambda.runtime.events.APIGatewayV2HTTPEvent;
import com.amazonaws.services.lambda.runtime.events.APIGatewayV2HTTPResponse;
```

当然还有相关的 Maven 依赖项(如果你坚持使用 Gradle，那就靠你自己了):

```
<dependency>
  <groupId>com.amazonaws</groupId>
  <artifactId>aws-lambda-java-core</artifactId>
  <version>1.2.1</version>
</dependency>

<dependency>
  <groupId>com.amazonaws</groupId>
  <artifactId>aws-lambda-java-events</artifactId>
  <version>3.8.0</version>
</dependency>
```

类定义是`RequestHandler`的一个实现:

```
public class Handler implements RequestHandler<APIGatewayV2HTTPEvent, APIGatewayV2HTTPResponse> {
}
```

这要求我们实现一个单一的功能:

```
@Override
public APIGatewayV2HTTPResponse handleRequest(final APIGatewayV2HTTPEvent event, final Context context) {
}
```

很明显，`APIGatewayV2HTTPEvent`和`APIGatewayV2HTTPResponse`分别包装了请求和响应。`Context`对象包含关于 Lambda 及其环境的细节，比如函数名和 ARN，但它也给你提供了对`LambdaLogger`的访问:

```
context.getLogger().log("this message shows up in the console")
```

如果 Lambda 被连接到 CloudWatch，那么日志消息会被发送到那里，否则它们会被发送到 Lambda 控制台。当你写代码的时候，你不需要知道*的任何事情。但是请注意，当您测试处理程序时，您需要注入一个模拟上下文，并且在测试期间提供一个模拟`LambdaLogger`来记录到您的本地日志环境中也是很方便的。AWS Lambda 开发人员指南包含了一些有用的例子。*

传入的`APIGatewayV2HTTPEvent`包含相当多您可能需要处理请求的东西，但是不要让它压倒您。呈现为 JSON 的传入请求示例如下:

```
{
    "version": "2.0",
    "routeKey": "GET /cidr/{region}/{service}",
    "rawPath": "/v1/cidr/eu-west-2/S3",
    "rawQueryString": "ipv6=true",
    "headers": {
        "accept": "*/*",
        "content-length": "0",
        "host": "mqciw5p4x8.execute-api.eu-west-2.amazonaws.com",
        "user-agent": "curl/7.64.1",
        "x-amzn-trace-id": "Root=1-60a7a07d-39030559613c7c1754c2ff6d",
        "x-forwarded-for": "89.36.68.26",
        "x-forwarded-port": "443",
        "x-forwarded-proto": "https"
    },
    "queryStringParameters": {
        "ipv6": "true"
    },
    "pathParameters": {
        "region": "eu-west-2",
        "service": "S3"
    },
    "isBase64Encoded": false,
    "requestContext": {
        "routeKey": "GET /cidr/{region}/{service}",
        "accountId": "304388919931",
        "stage": "v1",
        "apiId": "mqciw5p4x8",
        "domainName": "mqciw5p4x8.execute-api.eu-west-2.amazonaws.com",
        "domainPrefix": "mqciw5p4x8",
        "time": "21/May/2021:11:58:53 +0000",
        "timeEpoch": 1621598333192,
        "http": {
            "method": "GET",
            "path": "/v1/cidr/eu-west-2/S3",
            "protocol": "HTTP/1.1",
            "sourceIp": "89.36.68.26",
            "userAgent": "curl/7.64.1"
        },
        "requestId": "frYDkj2TrPEEMWw="
    }
}
```

幸运的是，您不必处理 JSON 的任何一部分，或者所有这些细节，因为请求/响应对象为您提供了有用的方法。

出于我的目的，我需要看到被调用的 HTTP 路径、路径参数(如果有的话)和查询参数(如果有的话)。

获取路径？简单(但注意它不包括和查询参数):

```
String rawPath = event.getRawPath();--> "/v1/cidr/eu-west-2/S3"
```

测试是否有查询参数“ipv6=true”也很简单，因为`getQueryStringParameters()`给了我们一个`Map<String, String>`

```
boolean ipv6 = Boolean.*parseBoolean*(
    event.getQueryStringParameters()
         .getOrDefault("ipv6", "false")
);--> false
```

路径参数也是如此。我有“服务”路径参数吗？

```
String service = event.getPathParameters()
    .getOrDefault("service", null);--> "S3"
```

这足以让我调用我的库来计算响应。构建响应很简单(注意，我使用 Google 的 [GSON](https://github.com/google/gson) 序列化为 JSON):

```
Gson gson = new GsonBuilder().setPrettyPrinting().create();responseData = <<calling my library here>>return APIGatewayV2HTTPResponse.*builder*()
        .withStatusCode(200)
        .withHeaders(Map.*of*("Content-Type","application/json"))
        .withBody(gson.toJson(responseData))
        .build();
```

或者，如果我想建立一个错误响应:

```
return APIGatewayV2HTTPResponse.*builder*()
        .withStatusCode(503)
        .withHeaders(Map.*of*("Content-Type", "application/json"))
        .withBody("{}").build();
```

在这种情况下，我仍然返回一个空的 JSON 对象，只是为了在错误响应和良好响应之间保持一致。

AWS API Gateway 有很多令人喜欢的地方，尤其是新版本。它结合了强大的功能和灵活性，以及易于设置、部署和管理的优势。将它与 Lambda 函数结合起来，会产生一种架构，它消除了传统 API+后端服务解决方案的许多麻烦。对于许多用例来说，这是一个理想的解决方案，因此令人失望的是，现有的文档和教程材料太差了，以至于学习曲线变得不必要的陡峭。