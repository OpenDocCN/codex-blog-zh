# CDK、Python 和 moto

> 原文：<https://medium.com/codex/cdk-python-and-moto-5e8ddffb5779?source=collection_archive---------15----------------------->

![](img/f0ccdb5c4a1c44fb38e37ee38956caa2.png)

## 技术概述

## 模仿 CDK 云构造配置的 AWS 基础设施

云开发工具包，或称 CDK，是亚马逊在云形成失败后的第二次尝试，旨在创建一个舒适而实用的部署框架。这里是你如何测试它。

# 云形成:打个招呼

当 web 开发慢慢转向无服务器架构时，第一个也是唯一一个 AWS Lambda 是亚马逊实现历史转折的核心。从逻辑上来说，从裸机到虚拟化，再到容器化，物理计算环境的细节逐渐消失，这给了开发人员一个专注于问题的领域细节的机会。当然，至少在理论上是这样。几十年前，个人计算领域也发生了类似的转变，例如，允许操作系统在虚拟内存领域工作，应用程序开发人员不再关心物理设备上的内存分配。

使用任何程序的核心——函数，推动分布式应用程序的开发人员使用本地 SaaS 解决方案来完成典型任务:ElasticCache 用于缓存，RDS 用于关系数据库，DynamoDB 用于基于文档的大型数据库，EFS 用于本地文件系统。亚马逊服务的设置类似于一台分布式计算机。与 docker 方法类似，这需要一个声明性的基础设施描述，因此亚马逊提出了 CloudFormation，这是一个基于所谓模板的巨大云配置引擎。可以使用 JSON 或 YAML 编写模板。下面是一个使用 CloudFormation 模板的 DynamoDB 数据库的示例。

DynamoDB 的云形成 Json 模板

CloudFormation 于 2011 年首次发布，与 AWS 服务组合一起成长，现在它支持几乎所有的技术。

云形成支持技术的完整列表可以在[这里](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)找到。

像许多先驱一样，造云技术不得不犯所有的错误，而且从未成为一项被广泛接受的技术，这可以通过 terraform/terragrunt 和 CDK 的存在这一事实来证明。庞大的配置和它所基于的被阉割的模板语言使得有点严肃的基础设施的冗长的配置几乎不可读。2019 年，AWS 提出了 CDK。

# CDK:回到“编程”

云开发工具包(v2)，或 CDK，将基础设施作为整个级别的代码方法。CDK 没有用各种各样的 tomls、YAML、jsons 和更多的*mls 脱离实际编程，而是把基础设施当作代码，抱歉这是同义反复。

标记语言，不管做得多好，都不会让开发者创建真正编程语言的复杂结构:条件、循环、函数等等。这就是它们存在的实际目的:例如，将逻辑从配置中分离出来，让不太精通核心开发的操作工程师和开发人员自己修改软件属性和行为，而无需对实际软件重新编程。

将标记语言用于基础设施的方法似乎很合理:为什么我们需要 docker compose config 或 CloudFormation 模板中的循环？这仅适用于相对简单的拓扑结构。只要你想象一个庞大的企业基础设施，你就会立即需要像函数和模块这样的东西来保持代码干燥，循环类似性质的对象，比如 lambdas，或者为分布式应用程序的不同环境设置条件语句。许多标记语言实际上提供了这些特性的基本模拟，但没有一种语言能与真正的编程语言的表达能力相提并论。

所以 CDK 是这一原则的体现。凭借 CloudFormation 模板，CDK 为各种平台提供了语言原生 SDK:JavaScript/Typescript、Java、Python、。撒网就走。与 CloudFormation 一样，核心原则是堆栈:对象和资源的集合，它们通过目的和使用模式相互关联。栈是从官方库的 Stack 类继承的。这里有一个真实世界的例子，一个 SQS 触发器+哨兵的 lambda 创建。

Python 库中的 Stack 类没有实现，类声明只是 jsii 库之上的 JavaScript 中真正实现的一个门面，下面是 stack 类头的样子:

```
@jsii.implements(ITaggable)
class Stack(
    constructs.Construct,
    metaclass=jsii.JSIIMeta,
    jsii_type="aws-cdk-lib.Stack",
):
...
```

使用真正的编程有它所有的好处，重复的指令可以包装在一个函数中。单元测试带来了额外的好处:CDK 将堆栈渲染为云形成模板，你可以根据它运行你的断言，而无需将堆栈部署到真正的云上。很整洁哈？开箱即用的断言非常基本，所以这里有几个例子和一些小的实用函数:

这就是代价:你只能在云中运行你的应用程序。怎么办？

# SaaS 的主要交易

虽然与 Kubernetes 上的自有云相比，使用 SaaS 和部署 CDK 及类似服务是低样板解决方案，但它的价格(标签)很高。像 Amazon 这样的公司会尽一切努力将您的解决方案包含在 Amazon 宇宙中，尽可能艰难地迁移到另一个解决方案。

对于像 AWS 这样的目标平台，在某个时候你必须决定放弃本地开发，因为罕见的 AWS 服务可以在本地运行和测试，对于 Python + Postgres + Redis stack 来说如此自然的本地开发变成了一种真正的奢侈。您要么需要维护 docker compose 和 AWS 设置，这是不合理的，要么决定只在一个平台上测试。

这种二元性的一个显著解决方案是 [moto](https://github.com/spulec/moto) :一个巨大的库，模仿你的 python 代码中的许多 AWS 服务，使带有巨大 AWS 基础设施的 python 代码的单元测试变得轻而易举。Moto 在内存中创建了一个完整的 AWS 云，你可以在这里对你的队列、数据库和通知进行简单的测试，而不必使用真正的基础设施。

基于云的测试有以下重大问题:

*   缓慢:一个包含 200-300 个测试的测试套件将在云上运行 30 分钟，而使用 moto 只需 1-2 分钟
*   不孤立:如果其他开发人员同时开始测试，您的两个测试很可能都会失败
*   不可重复:在真实基础设施上的测试将不可避免地留下工件，如果执行不完整并且清理脚本没有生效的话

> 从上面可以看出:开发人员不会足够频繁地执行测试并创建新的测试，因为他们会随机失败并且花费太长时间

moto 的主要代价是实际的嘲笑:执行路径有时会很棘手，所以找到你漂亮的模仿 s3 不工作的原因可能需要一些时间。

# 用摩托嘲笑整个集群

在其他服务中，moto 支持 CloudFormation，尽管不是完全支持。[这里是支持的功能列表](http://docs.getmoto.org/en/latest/docs/services/cloudformation.html)。

通常使用 moto，您会用 pytest 生成器夹具来模拟服务，如下所示:

```
@pytest.fixture(scope="session")
def s3_mock():
    with mock_s3():
        import boto3

        yield boto3.client("s3", region_name="eu-central-1")
```

注意 yield 部分，如果你想让 mocking 正常工作，所有其他使用这个的设备都必须是测试用的生成器。

首先，你需要为你的 CDK 创建一个云信息模板:

```
cdk synth > template.yaml
```

这个模板包含了你所有的云基础设施，包括 lambdas 和它的代码。

命令创建了一个 CloudFormation 堆栈，它描述了 CDK 工作所需的所有基础设施，比如存储 lambdas 代码的 s3 存储桶。它本身就是一个引导装载程序。我未能使这个模板工作，所以这里是 pytest fixture 手动创建必要的基础设施，并修复我在加载我当前项目的 CloudFormation 模板(lambdas，SQS 触发器)时不得不面对的各种错误:

需要各种各样的技巧，比如用代码创建虚构的拉链等等。Moto 之前没有尝试过这种场景。此外，处理一个中等大小的配置需要 20-30 秒，这使得它无法用于单元测试。不过，中间集成测试是可以想象的。

# 从 CDK 和 Python 开始的一些链接

入门:[https://docs . AWS . Amazon . com/CDK/v2/guide/work-with-CDK-python . html](https://docs.aws.amazon.com/cdk/v2/guide/work-with-cdk-python.html)

教程:[https://hands-on . cloud/how-to-use-AWS-CDK-to-deploy-python-lambda-function/# source-code](https://hands-on.cloud/how-to-use-aws-cdk-to-deploy-python-lambda-function/#source-code)

=====================================

如果你喜欢这个，看看我最近的文章:

🐘 [Django-plpy](/codex/django-plpy-717a5f4644dc) :用于 PostgreSQL 中 Python 存储过程的 Django 工具包

🕒为什么你的软件质量会随着时间的推移而下降

😃 [RapidAPI:使用 Python 的第一步](/analytics-vidhya/rapidapi-and-fastapi-d720789a5b7e)