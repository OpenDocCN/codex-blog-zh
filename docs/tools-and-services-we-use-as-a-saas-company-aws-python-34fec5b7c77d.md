# 我们作为 SaaS 公司使用的工具和服务— AWS + Python

> 原文：<https://medium.com/codex/tools-and-services-we-use-as-a-saas-company-aws-python-34fec5b7c77d?source=collection_archive---------5----------------------->

在成为 [propellor.ai](https://www.propellor.ai/) 的 CTO 之前，我在不同的初创公司担任过助理工程师、产品经理、数据科学家和 AWS 解决方案架构师，包括产品开发和咨询。

我收集了一些 GitHub 库、文档、最佳实践、网站和工具，它们帮助我们努力扩展、快速跟踪和提高效率。我还年轻，还在学习，我愿意与那些可以从这些技术和工具中受益的人分享我所学到的东西。永远记住 AWS 的名言

> 没有经验的压缩算法

以下部分构成了整个博客:

1.  开发堆栈
2.  测试工具
3.  可观察性工具
4.  最佳实践和标准
5.  生产力工具
6.  超出范围

# 开发堆栈(DevOps 设置)

作为领导者，你的工作是建立技术栈并进行增量调整，而不是返工。在做出任何决策之前，请确保您了解自己面临的挑战，与业务部门交流，了解您团队的现有能力、上市时间和应用路线图。

> 想法是不要太大或太小。你必须在**和**之间找到适当的平衡。花时间阅读博客，与专家交流，联系 AWS 寻求帮助，等等。

**前端:**

>我们使用的前端框架是有角度的。(React 可能更好，也可能不太好，但是请记住，您选择的框架应该基于您的团队已经拥有的技能，而不是基于可用的最佳工具。)永远不要因为某个 YouTube 互联网专家说你用了废话就选择一个工具…

>我们使用 [Ng Ant](https://ng.ant.design/docs/introduce/en) 作为我们的设计框架。

> [echarts](https://echarts.apache.org/en/index.html) 为建筑图

**中间件**:

作为一家初创公司，你的预算和资源是有限的。每个部门不可能有几十个中小企业。因此，为了完成任务，您应该尽可能多地依赖托管服务。Amazon Web Services、Google Cloud Platform 和 Microsoft Azure 等基础设施公司为您管理基础设施服务，让您只关注业务逻辑。我们在 AWS 上选择了无服务器来管理我们的所有工作负载。

无服务器提供三种主要功能:

1.  它负责基础设施的可用性，因此您可以专注于编写代码(即业务逻辑)
2.  它处理基础设施的可扩展性，因此您不必担心负载平衡或为您的应用程序配置服务器。
3.  它很划算，因为你只需为你使用的东西付费。

> 要开始使用 AWS serverless，请阅读[本](https://aws.amazon.com/blogs/compute/getting-started-with-serverless-for-developers-part-1/)。

要创建您的应用程序，您可以使用 AWS SAM 或 AWS SDK。然而，要开始，先从一个基本的 lambda 函数和 API 网关开始。

> 我可以应要求提供更详细的基础设施文章。

**后端(数据工程+数据科学)**:

超出范围**

# 测试工具

>我们使用[蝗虫](https://locust.io/)进行性能测试(开源)——基于 python

>用于 HttpUser 行为测试的含硒蝗虫— [蝗虫插件](https://github.com/SvenskaSpel/locust-plugins)

> [Thunder client](https://marketplace.visualstudio.com/items?itemName=rangav.vscode-thunder-client) 用于 API 开发和测试的 VScode 扩展——这是 postman 开源替代方案

> [endtest](https://endtest.io/) 通过 CI/CD 管道实现自动化保持测试的工具——付费工具

Google light house——最被低估但真正有价值的——开源

> JSON 工具，如 [JSON 查看器](http://jsonviewer.stack.hu/)、[jsoneditoronline.org](https://jsoneditoronline.org/)、[arraythis.com](https://arraythis.com/)

# 可观察性工具

[Lambda power tools Python](https://awslabs.github.io/aws-lambda-powertools-python/latest/)—AWS Lambda 函数的一套实用程序，用于简化采用最佳实践，如跟踪、结构化日志记录、自定义指标等。

[AWS X 射线](https://aws.amazon.com/xray/) —开发人员使用服务地图和跟踪来分析和调试应用程序。

[cloudwatch 日志](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CloudWatchLogsConcepts.html) —通过使用这个来理解日志记录实践

Grafana 是一个非常棒的工具，但对于关键信息，我仍然建议使用 cloudwatch 指标、见解和仪表板。每个应用程序都是不同的，没有办法用一个模板仪表板来解决您的问题。Datadog 也是一个很棒的工具，但是如果你使用 AWS serverless，powertools 就足够了。

# 最佳实践和标准

1.  12 因素网络给出了我们需要跟踪的整个清单。— [12 个工厂网](https://12factor.net/)
2.  AWS 架构的 5 大支柱(现为 6 大支柱)——[AWS 5 大支柱](https://aws.amazon.com/blogs/apn/the-5-pillars-of-the-aws-well-architected-framework/)
3.  API 命名约定对于发展应用程序和减少发布混乱是必不可少的。我们遵循[白宫](https://github.com/WhiteHouse/api-standards)的标准
4.  标准设计页面和元素清单— [设计清单](https://www.checklist.design/)
5.  无服务器设计模式和最佳实践在—[Serverlessland.com](https://serverlessland.com/)可用
6.  “牛逼列表”是每篇编码技术文章的必备。下面是我个人比较喜欢的几个:
    ——[Awesome-python](https://github.com/vinta/awesome-python)
    ——[Awesome-JavaScript](https://github.com/sorrycc/awesome-javascript#readme)
    ——[Awesome-Angular](https://github.com/PatrickJS/awesome-angular#readme)
    ——[https://github.com/TheJambo/awesome-testing](https://github.com/TheJambo/awesome-testing)
    ——[https://github.com/aws-samples/aws-serverless-workshops](https://github.com/aws-samples/aws-serverless-workshops)

# 生产力工具

[JIRA](https://www.atlassian.com/software/jira)——冲刺规划、错误跟踪和智能提交*。

[Basecamp](https://basecamp.com/) —实时协作，在留言板上分享最佳实践、会议记录、业务待办任务等

[观念](https://www.notion.so/) —开发者内部对话、特性文档、软件规格文档

Google sheets——一个在一切进入格式的大本营或概念之前快速转储一切的地方。此外，永远不要估计电子表格的威力

[计时](https://clockify.me/) —用于休假管理和任务时间跟踪

[docusaurus](http://docusaurus.io) —构建产品文档

[Figma](https://www.figma.com/) —开发 UI 屏幕和原型

# 超出范围

我没有介绍核心 AWS 无服务器，因为我们使用超过 42 种不同的 AWS 服务，不可能一次全部介绍完。此外，架构实践、无服务器分析引擎开发、无服务器 SDK 是我打算在不久的将来写的一些文章。