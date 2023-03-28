# Kubernetes 的 DevOps 安全清单

> 原文：<https://medium.com/codex/devops-security-checklist-for-kubernetes-3672b4ee8288?source=collection_archive---------0----------------------->

![](img/8a5e62f8f4abcf146f743b2873da3fbe.png)

由 [Unsplash](https://unsplash.com/s/photos/security?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[Ruben Bagües](https://unsplash.com/@rubavi78?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

Kubernetes 是当今许多公司采用的容器编排平台。它的实施需要对其生态系统有一定的了解，以便部署一个可用于生产的集群。因此，实施工作原则或工具至关重要，需要所有团队(运营、开发、安全等)的共同努力。)以促进尽快检测异常，从而提高编排器及其资源的安全级别。

# 提交前挂钩

通过在持续集成管道中尽可能早地添加自动化流程，将对生产的影响降至最低，这是当今 DevSecOps 世界公认的原则。

引入这种称为“向左转移安全性”的实践是为了促进开发、安全性和操作团队之间的协作。其思想是通过将安全性和测试流程移到传统线性 SDLC 表示的左侧，从添加预提交开始，在开发周期的早期确保应用程序的安全性。

近年来出现了一些工具来促进这种集成，以便:

*   格式化 YAML 文件代码
*   检测 Kubernetes 资源配置中的异常
*   强制配置和安全策略的应用尊重良好的开发实践
*   在提交任何源代码之前检测敏感数据

以下是控制 YAML 定义文件的三个很好的工具示例:

*   [YAML 皮棉](https://github.com/adrienverge/yamllint)
*   [Checkov](https://www.checkov.io/4.Integrations/pre-commit.html)
*   [k8s 验证](https://github.com/Agilicus/pre-commit-hook-k8svalidate)

# 持续集成检查

提交前测试很少能被 DevSecOps 团队强制执行，它们通常被推荐用于促进团队合作。在某些情况下，预提交任务的实现可能会很麻烦和繁重，尤其是对于大型团队。这些测试仍然是必要的，因此必须在持续集成过程中进一步推进。

提交前的任务可以分为，首先，格式化代码，然后，扫描它以验证配置文件的一致性。

上一节提到的工具仍然是不错的选择。这个列表中还可以添加一个简单易用的 web 平台 [Datree](https://www.datree.io/) ，它允许 DevSecOps 团队轻松地开发、版本控制、查看和执行安全规则，以确保在部署之前遵守 YAML 文件。

# 图像扫描

甚至在部署之前扫描图像是一个重要但有时被忽视的步骤，因为许多人认为官方图像是安全的。然而，对其进行扫描是很重要的，因为每天都会发现新的漏洞，并且更新任何具有安全漏洞的系统以限制恶意人员攻击的范围也是很重要的。

这些扫描必须在容器生命周期的不同阶段进行

*   在将映像发布到远程注册中心之前，确保所讨论的映像即使在部署之前也符合安全规则
*   在容器运行期间，尽可能快地识别需要重建的映像，以便纠正新识别的漏洞

可以使用几个开源工具来收集这些数据，并将发现的问题通知团队，例如:

*   [繁琐](https://github.com/aquasecurity/trivy)
*   [神经矢量](https://github.com/neuvector/neuvector)

# 聚类扫描

确保 Kubernetes 集群的安全取决于公司的安全治理。应用的策略必须考虑可访问性、维护、数据管理等。

尽管如此，重要的是要遵守社区确定的一些规则，以确保在安装集群时有一个良好的基本安全级别。还建议定期扫描 Kubernetes 集群，以便在运行时识别任何已知的异常，主要是与其配置相关的异常。

有几种工具可以自动生成异常检测报告，例如:

*   由 Aqua Security 开发的右舷
*   [由 VMware 开发的声纳浮标](https://sonobuoy.io/)

# 安全上下文

Kubernetes 安全上下文遵循最小特权原则:一个主体应该只获得他执行任务所需的特权。

安全上下文是一种工具，允许管理员根据每个资源定义与安全相关的参数。这允许每个资源被给予它访问主机服务器上的资源所需的特定权限，同时拒绝它不特别需要的资源的访问。在 Kubernetes 上下文中，安全上下文定义了 pod 中各个容器的权限。

管理安全上下文需要对集群、其安装和生态系统进行高级管理和理解。尽管实施起来很复杂，但是这些措施仍然是限制任何运输舱或集装箱操作的非常有效和自然的方法。

# 基于角色的访问控制

利用 Kubernetes 基于角色的访问管理(RBAC)是保护运行在该平台上的集群和应用程序的第一步。RBAC 原则非常简单:根据用户身份定义谁可以访问什么。

Kubernetes 拥有高效的粒度来管理对不同资源的访问:

*   用户访问用户帐户中的结果
*   应用程序访问导致服务帐户
*   角色来定义仅限于单个命名空间的资源的权限
*   应用集群级限制的集群角色

这种粒度与外部身份提供者(如 Okta、Gmail、LDAP 等)相结合。)允许对访问进行非常精细的管理，从而确保对资源的控制，并且通常确保其可审计性。

# 网络策略

网络安全规则的管理可以是安全左移概念的一部分。Kubernetes 网络策略允许管理员和开发人员使用规则强制执行哪些网络流量是允许的。左移原则允许开发人员在不理解底层网络概念的情况下安全地访问他们的应用程序。

DevOps 团队可以强制执行默认策略，开发人员可以管理特定的访问权限，让他们在管理应用程序时有一定的自主权。

网络策略由部署在群集上的容器网络接口(CNI)控制。一些 CNI 提供了这一功能，但这里有两个值得特别注意:

*   Calico ，可能是库伯内特生态系统中最著名和最常用的 CNI。在这个免费版本中，Calico 可以管理这些高达 OSI 模型第 3 级的网络规则。要管理更高级别的，就要用付费版。
*   [Cilium](https://cilium.io/) ，一个很好的替代品或附加组件，在 OSI 模型的所有 7 层上提供了许多免费功能。

# 政策实施

Kubernetes 是一个主要基于 API 的应用。这种方法使得开发对这些不同资源的访问控制工具成为可能，以便对它们进行审计和保护。

准入控制器是 Kubernetes 客户机(如 Kubectl)发出的所有请求的入口点。在这个阶段添加检查点允许您在执行之前验证所有查询，从而防止任何偏离公司安全治理的行为。

这些控制点可用于:

*   检查是否设置了 CPU 和内存限制
*   确保用户不会更改默认网络策略
*   确保特定资源始终包含特定标签
*   拒绝对特定资源的权限
*   防止使用最新的标签
*   为每个新命名空间生成默认网络策略

目前可以使用几种工具来管理这些安全规则:

*   开放策略代理 (OPA)可能是在 Kubernetes 上执行策略的最著名的应用。OPA 策略是用一种叫做减压阀的高级声明性语言来表达的。
*   [Kyverno](https://kyverno.io/) ，它可以使用准入控制和后台扫描来验证、变异和生成配置。Kyverno 政策像任何 Kubernetes 资源一样在 YAML 文件中表达，并且不需要学习新的语言。

强烈建议在任何 Kubernetes 集群的基本配置文件中包含这些工具之一。

# 运行时威胁检测

Kubernetes 不是一个安全平台。它缺乏本机工具来处理大多数与安全相关的任务，如检测应用程序中的漏洞和监控违规行为。

实时检测异常或威胁是任何安全治理的一个重要方面。Kubernetes 平台也不例外。相反，它在数据管理等不同领域的广泛使用使其安全性成为其业务运营的关键点。

市场上出现了几种开源工具来识别容器和应用程序的任何偏离行为，例如:

*   由 Aqua Security 开发的 [Falco](https://falco.org/)
*   [神经向量](https://neuvector.com/products/open-source/)

两者都允许在运行时检测威胁，并发送由事件报告补充的警报，以采取必要的措施。

# 过时的资源

Kubernetes 因其灵活性和对生产中新功能交付的影响而成为当今 DevOps 领域的主要参与者。这个工具允许开发团队提高他们的部署速度，因此需要特别注意所使用的资源，不要用过时的资源损害集群的安全性。

有不同类型的过时资源:

*   弃用的 Kubernetes APIs
*   旧的/废弃的 Kubernetes 资源(如 Helm 版本)

被否决的 Kubernetes APIs 不一定是安全漏洞，但它们会影响集群的生命周期，从而危及集群的维护。 [Pluto](https://github.com/FairwindsOps/pluto) 是由 [Fairwinds](https://www.fairwinds.com/) 开发的一个实用程序，用于帮助用户在他们的代码库和 helm 版本中找到被弃用的 Kubernetes apiVersions。这种类型的分析必须在更新集群之前执行，以确保其成功并防止潜在的安全漏洞。

确保对 Kubernetes 集群的保护还包括扫描活动资源。作为一名包经理，Helm 需要特别注意检查图表的生命周期，并遵循管理资源的每周或每月更新计划。 [Nova](https://github.com/FairwindsOps/nova) ，Fairwinds 开发的另一个工具，使这种类型的扫描自动化成为可能，以便快速检测需要更新的图表，或者如果图表已经过时，甚至从集群中删除。

# 下一个？

有关更多信息，请参考本文档:

*   开发工程师应该知道如何控制 Kubernetes
*   [10 Kubernetes 你应该了解的安全上下文设置](https://snyk.io/blog/10-kubernetes-security-context-settings-you-should-understand/)
*   [库伯内特 RBAC](https://sysdig.com/learn-cloud-native/kubernetes-security/kubernetes-rbac/)

# 关于作者

[Nicolas Giron](https://www.linkedin.com/in/nicolas-giron-6129b0a1/) —现场可靠性工程师(SRE) — DevOps