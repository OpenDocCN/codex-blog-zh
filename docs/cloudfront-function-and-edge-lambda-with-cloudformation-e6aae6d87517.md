# 具有云形成的云锋、函数和边缘@λ

> 原文：<https://medium.com/codex/cloudfront-function-and-edge-lambda-with-cloudformation-e6aae6d87517?source=collection_archive---------20----------------------->

最近，我把我们的登陆/博客页面从 EC2 服务器转移到了 S3 的一个静态网站，并通过 CloudFront 提供服务。该站点有多个重写配置(692)，我必须随之迁移重写配置。

我最初的想法是使用 **S3 网页重定向**。但是它有每个桶 50 个重定向的限制，我必须编写 692 个重定向规则。

后来，我决定使用 **Cloudfront 函数**，它类似于 Lambda 函数，成本更低，有 200 多个边缘缓存。此外，它的执行时间(1 毫秒)和内存(2MB)也更少。而 **Edge@Lambda** 执行时间从 5 秒(查看器触发)到 30 秒(原点触发)和 128MB(查看器触发)内存。Cloudfront 函数只支持 **10KB 的包大小**并且**不能访问请求体**。

我编译的包有 64KB，我不得不使用 Edge@lambda。总之，在这篇博客中，我已经包括了 Cloudfront 功能部分及其与 Cloudfront 发行版的关联

> 在本教程的中，我将使用 **cloudformation tamplate** 来部署 all stack 和 CICD 管道，以实现多种环境(中转和生产)的自动化和部署
> 
> 在这里找到云形成的完整代码[https://github.com/5sfayas/Cloudfront-Function-EdgeLambda](https://github.com/5sfayas/Cloudfront-Function-EdgeLambda)

## 云形成变量

从上面的代码中，我有两个部署，`DomainName: stage.fayas.com`用于阶段，`fayas.com`用于生产。为了指定部署阶段，我在模板中配置了参数。基于它的价值、暂存或生产，模板用相应的定制域名或 CNAME 和 ACM ARN 配置 CloudFront 发行版。

## CloudFront 函数

`Type: AWS::CloudFront::Function`属性用于创建 CloudFront 函数资源。
！Sub 用 EnvType 替换/替代参数。
`FunctionCode:`定义了 Cloudfront 功能代码和
`Domain: !FindInMap [EnvType, !Ref EnvType, DomainName]`！FindInMap 获取给定参数的**域名**，并存储到**域**变量中。`Fn::Sub:`是从给定参数中替换域的数组。

在这种情况下，如果我的 EnvType 是 stage，并且代码块如下所示。

```
var mapping = {"/who": "https://stage.fayas.com/fayas-akram/","/about-me": "https://stage.fayas.com/fayas-akram/","/home": "https://stage.fayas.com/","/root": "https://stage.fayas.com/"}
```

## 边缘@λ

正如我前面提到的，由于它的局限性，我不得不使用 Edge@Lambda 来代替 Cloudfront 函数。如果你的函数规模较小，请考虑使用函数，如果你想使用 Edge@Lambda 忽略以上配置。

Lambda 函数资源创建类似于 Cloudfront 函数，其中使用了`Code:`而不是`FunctionCode:`
`Type: AWS::Lambda::Version`定义的函数，当代码有新的变化时，它将创建一个新的 Lambda 函数版本。

## 云锋

我将创建一个带有自定义 s3 源域的默认缓存行为，Cloudfront 将为查看器请求触发 Edge Lambda。

为部署 CloudFront 堆栈而定义的资源`Type: AWS::CloudFront::Distribution`。`LambdaFunctionAssociations:`与我们之前创建的 Edge Lambda 函数相关的块。
`EventType: viewer-request` Cloudfront 将触发 Edge Lambda 进行查看请求。！连接函数将连接带有版本号的λARN。

`Origins:`定义了自定义 S3 源属性和`OriginProtocolPolicy:` http-only，这将把 http 流量发送到源

如果你想使用 Cloudfront 函数，使用`FunctionAssociations:`属性。

这就是基于 Cloudfront 的静态站点的内容缓存重定向。

在这里找到[的完整代码。](https://github.com/5sfayas/Cloudfront-Function-EdgeLambda)

谢谢！！！