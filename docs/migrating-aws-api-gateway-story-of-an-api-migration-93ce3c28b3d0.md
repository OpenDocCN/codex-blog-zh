# AWS API 网关的不可能性(API 迁移的故事)

> 原文：<https://medium.com/codex/migrating-aws-api-gateway-story-of-an-api-migration-93ce3c28b3d0?source=collection_archive---------9----------------------->

在工作中，我们必须将 API 网关从一个 AWS 帐户迁移到另一个帐户。迁移必须没有停机时间，并且是渐进的(慢慢地将流量从旧的 API 转移到新的 API)。由于我们不控制客户，我们不能要求他们逐步将呼叫转移到新的端点。
我们认为这很容易，但我们错了。

这个想法是将我们现有的域名(在下面的例子中是 app.domain.name.com)指向两个不同的 API 网关，旧的和新的。我们非常确定这可以通过一些简单的 DNS 更改来实现。这就是我们认为的解决方法:

*   在新帐户中复制 API 网关
*   向指向旧 API 网关的现有 DNS A 记录(在 Route53 托管区域中)添加权重
*   在解析到新 API 网关的 Route53 托管区域中插入一个具有权重的新 A 记录

换句话说，我们想从这个:

```
+---------+---------+----------------------+--------------------+
| Record  | Weight  | Source               | Destination        |
+=========+=========+======================+====================+
| A       | -       | app.domain.name.com  | API-GW-domain-old  |
+---------+---------+----------------------+--------------------+ 
```

对此:

```
+---------+---------+----------------------+--------------------+
| Record  | Weight  | Source               | Destination        |
+=========+=========+======================+====================+
| A       | 255     | app.domain.name.com  | API-GW-domain-old  |
+---------+---------+----------------------+--------------------+
| A       | 255     | app.domain.name.com  | API-GW-domain-new  |
+---------+---------+----------------------+--------------------+
```

添加相同的权重(本例中为 255)，使得 Route53 在两个记录之间以相等的概率进行选择。我们希望这就是我们如何慢慢地将流量从旧的 api 转移到新的 API。以下是尝试实现上述内容的结果、失败的尝试以及 API 网关和 Route53 集成中的误解。

我们首先在一个新的 AWS 帐户中创建了新的 API，只是偶然发现了一个 CloudFormation 故障。API 网关自定义域资源失败，因为另一个帐户中已经存在具有该域的自定义域。原来分配给区域端点**的域必须**是唯一的。因此上面的云形成失败了。旧 API 已经有一个包含该域的 API 网关自定义域资源(app.domain.name.com)。

很好，所以这是行不通的。我们尝试了另一种方法。创建新的 API 网关，并为其分配一个不同的自定义域，该域仅在最左侧的子域有所不同。即:

```
+-------+-------+------------------------+------------------------+
| Record| Weight| Source                 | Destination            |
+=======+=======+========================+========================+
| A     | 255   | app.domain.name.com    | api-old.domain.name.com|
+-------+-------+------------------------+------------------------+
| A     | 255   | app.domain.name.com    | api-new.domain.name.com|
+-------+-------+------------------------+------------------------+
| A     | -     | api-old.domain.name.com| API-GW-domain-old      |
+-------+-------+------------------------+------------------------+
| A     | -     | api-new.domain.name.com| API-GW-domain-new      |
+-------+-------+------------------------+------------------------+
```

api 域仅在子域上有所不同，这使得创建涵盖它们两者的证书(*.domain.name.com)变得简单。

因此，我们更新了证书，在新帐户中部署了新的 API(这次成功了)，创建了上表中描述的 DNS 记录。是时候验证它是否有效了。正是在这里，我们意识到通过“api-old.domain.name.com”访问旧 api 是可行的，通过“api-new.domain.name.com”访问新 api 是可行的……但通过“app.domain.name.com”访问应用程序却不可行。我们所期待的是新旧域之间的循环赛，我们得到的却是总是雄辩的 API GW 错误消息:`{"message":"Forbidden"}`。
为什么呢？看来*API 网关服务检查请求中的主机头，并期望它是 API 网关自定义域之一。换句话说，以下内容不起作用:*

```
> curl https://app.domain.name.com/demo {"message":"Forbidden"}
```

但这确实:

```
> curl -H "Host: api-old.domain.name.com" https://app.domain.name.com/demo {"message":"Hello, World!"}
```

最后，我们考虑使用一个[通配符自定义域](https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-custom-domains.html#wildcard-custom-domain-names)。这也不起作用，因为正如医生所说:

> *如果不同的 AWS 帐户创建了与通配符自定义域名冲突的自定义域名，则不能创建通配符自定义域名。*

文件还说有可能为此申请一个例外。吸取的教训是，将一个域名指向两个 API 网关是不可能的。那么如何迁移呢？一些选项包括:

*   让新的 API 发布 301 重定向到旧的 API
*   新 API 的旧 API 代理
*   部署一个代理，在旧 API 和新 API 之间转移流量
*   请求在两个不同的 AWS 帐户中使用通配符域的例外

*原载于 2022 年 3 月 24 日*[*https://dev . to*](https://dev.to/napicella/the-api-gateway-impossibility-1pho)*。*