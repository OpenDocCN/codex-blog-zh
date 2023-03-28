# Powershell 测试路径不过滤 AWS 存储网关文件共享

> 原文：<https://medium.com/codex/powershell-test-path-does-not-filter-on-aws-storage-gateway-file-shares-20f260033a1a?source=collection_archive---------11----------------------->

相反，测试完整路径，因为它也接受 GLOB 模式。

微软的 Powershell 是在微软视窗系统上进行开发的必备软件。最近，微软也让 Powershell 在 Linux 和 Unix (MacOS)上运行。

Powershell 可以做的许多事情之一是移动文件。在我们告诉它移动任何东西之前，我们要确保文件存在。Powershell 允许我们使用它的 Test-Path commandlet 来完成这项工作。

Windows 服务器上 Test-Path 的优点是它的过滤器属性。这使得 Powershell 通过匹配文件名模式来测试文件。这些模式可以包含 GLOB 模式，如*。过滤发生在包含文件的服务器上。这使得文件检测速度非常快。

> $ does _ exist = Test-Path \ \ server \ folder-Filter name-*。pdf-路径类型叶

遗憾是，当文件通过 AWS 文件存储网关托管在文件共享上时，过滤属性不起作用。我们艰难地发现了这一点:通过反复试验。我们只能猜测原因:可能是由于运行 Linux 的存储服务器，或者可能是特定的发行版没有安装完整的 Powershell 核心。不管什么原因:过滤器不起作用。

幸运的是，Test-Path 允许我们将进入其过滤器属性的值放入其默认属性(路径，文件位置)。它也接受 GLOB 文件名模式。唯一的缺点是它会慢很多，因为 Powershell 必须在客户端完成所有的匹配，因此必须首先列出所有可用的文件。

> $ does _ exist = Test-Path \ \ server \ folder \ name-*。pdf-路径类型叶

这变得很明显，因为我们发现有必要开始使用 AWS 文件存储网关。这是一个很棒的工具，它将 AWS S3 桶中的对象带到任何需要访问文件的工具的指尖。这意味着我们可以让供应商使用公共 REST API 上传他们的文件，将文件放入 S3，在 AWS Lambda 中运行检查和验证，并将接受的文件放在存储网关服务器获取它们并使它们可用于后端处理的位置。

听起来像是一项任务？我们同意。但是它速度快，价格合理，而且可靠。

# 关于作者

Veltstra 是一名专业的软件工程师和解决方案架构师，已经工作了近 25 年，在过去的 7 年中，他一直专注于集成数据和信息系统。

> 总有办法的。有时候，这条路比想象的要难走。