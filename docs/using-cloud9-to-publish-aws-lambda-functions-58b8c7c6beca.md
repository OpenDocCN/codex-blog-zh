# 使用 Cloud9 发布 AWS Lambda 函数

> 原文：<https://medium.com/codex/using-cloud9-to-publish-aws-lambda-functions-58b8c7c6beca?source=collection_archive---------14----------------------->

![](img/65afee7c1dff74911e84d64ef8753bc8.png)

我喜欢 AWS Cloud9。这是一个方便可用的 IDE，启动时间不会太长，不需要机器上的高要求资源，还可以从不同的计算机连接并继续您离开的地方。与本地安装的 IDE 相比，这是一项技术进步。

我已经开始为小工具编写大量的 Lambda 函数。也就是说，每个函数实现一个工具。我在编写它们时面临的一个挑战是快速编辑和部署每个 Lambda 函数。显然，Lambda 控制台不足以快速编辑几十个功能，因为你要么必须打开几个选项卡，要么必须降低生产率以等待每个页面加载。

这根本不是一个理想的解决方案。

幸运的是，AWS 已经将 Lambda 函数管理集成到 Cloud9 IDE 中，这样就可以在不引导 IDE 的情况下将您的代码上传到 Lambda。但这在某些方面仍有不足。

首先，如果你犯了一个灾难性的错误，这个错误在你的所有功能中都是重复的，你必须立刻修复并部署它，那该怎么办？使用 Cloud9 接口，你仍然需要指向并点击每个函数，点击按钮将每个函数上传到 Lambda，点击它们的目录和 ZIP 包，等等。所以当你有很多函数的时候，这并不是 Lambda 控制台的很大改进。

说到 ZIP 包，它们必须以一种特殊的方式创建才能上传到 Lambda。事实上，ZIP 包和根文件夹是将模块依赖项与函数捆绑在一起的唯一方式，因为 Lambda 控制台仍然没有为您提供从代码编辑器添加外部依赖项的简单方法。

因此，考虑到所有这些都是艰苦的工作，并且随着更多功能的创建，工作预计会达到不可管理的程度，因此必须有一种更简单的方法来做到这一点。

# 自动化拯救世界

我们可以在 Cloud9 环境中使用简单的 bash 脚本自动压缩所有的函数文件夹，并将它们上传到各自的函数中。

为此，我们需要安装 AWS CLI。在运行 Amazon Linux 的 Cloud9 上，默认安装，但它是版本 1。我个人更喜欢版本 2，在他们的文档中有安装它的说明[。但是，请确保如果您最终安装了版本 2，您要么在 PATH 变量中将它的文件夹向前移动(我的文件夹安装在/usr/local 下),要么使用`yum remove awscli`卸载版本 1，然后根据需要创建到/usr/bin/aws 和/usr/bin/aws_complete 的符号链接。当然，还要重启你的外壳。](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)

在使用 AWS CLI 之前，我们需要为 AWS 生成一个访问密钥，该密钥可用于非交互式脚本命令。这可以在 IAM 控制台中创建[。您将需要访问密钥 ID 和秘密密钥，因为您将在运行`aws config`时填充它们。](https://console.aws.amazon.com/iam/home?region=us-east-2#/security_credentials$access_key)

## 剧本

我创建了一个脚本，用于将所有文件夹压缩到它们自己的 zip 文件中，另一个脚本用于将每个 zip 文件夹上传到相应的 Lambda 函数中。这个特殊的脚本很简单，它假设 Lambda 函数被命名为名称的最后一部分是目录名。这两个脚本都应该放在~/环境文件夹中。

另一个函数比这个简单得多，它使用 AWS CLI 将 lambda-func.zip 文件上传到 lambda 函数。在使用 CLI 之前，请记住使用您的 API 密钥对其进行配置。

# 但是测试呢？

当然，在彻底测试之前，任何功能都不能投入使用。Lambda 在控制台中提供测试事件来创建和运行函数，但是我还没有找到在 CLI 中复制它的方法。关于这一点，我确实问过一个 [StackOverflow 问题](https://stackoverflow.com/questions/68231606/is-there-a-way-to-run-all-lambda-test-cases-simultaneously)，除了建议在我的测试事件中从脚本和 CLI 运行 Lambda 函数之外，没有得到太多的活动，所以我可能会尝试那个解决方案。