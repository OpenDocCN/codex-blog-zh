# 当您的服务在 Kubernetes 中运行时的数据库迁移

> 原文：<https://medium.com/codex/database-migration-when-your-service-is-running-in-kubernetes-abbe9697421d?source=collection_archive---------2----------------------->

![](img/570d45cd680356b7ff92964768f6b7c8.png)

只要服务需要永久的持久层，数据库迁移就是每个开发人员都必须面对的话题。对于关系数据库，模式是必需的，并且通常会随着时间的推移而发展，因为需要持久性的服务会发生变化。因此，人们将面临数据库迁移的挑战。

最常见的方法是使用迁移工具(或从头开始)，这将创建一个包含数据库版本的附加表。运行迁移时，通常会应用某种类型的数据库锁来避免并行执行迁移。

在 Kubernetes 上下文中运行服务会使数据库迁移变得棘手。

让我们来看一下在何时何地运行迁移的可能解决方案。

# 选项 1:在服务启动期间

许多迁移工具都以库的形式提供，可以在您喜欢的语言的代码中使用。

最简单的方法是在服务的启动阶段运行数据库迁移。

优点是设置简单，不需要额外的配置。然而，问题是，一旦您运行多个 pod 服务，您就会遇到困难。一个单元将尝试启动和运行迁移，但由于另一个单元已经在运行它而失败。这将导致服务无法启动，因此会在集群中引发错误事件，并强制多次重新启动，直到另一个 pod 完成迁移。另一个问题可能是超过设置的启动时间限制的大型迁移，导致 Kubernetes 集群认为您的服务无法启动。

# 选项 2:使用 initContainers 运行迁移

Kubernetes 提供了一个名为 [*initContainers*](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) 的功能，可用于部署。InitContainers 在每个 pod 开始之前执行一次(在进行部署时)。在 initContainer 成功完成之前，部署的 pod 不会启动。如果 initContainer 没有成功完成，它将重新启动，直到成功。

如果我们在 initContainer 中运行数据库迁移，它将在服务启动之前单独执行。这具有将服务从数据库迁移逻辑中分离出来的优点。然而，由于一个 pod 获得一个 initContainer，我们遇到了与选项 1 相同的问题。一旦我们同时部署多个单元，一次只能有一个单元运行迁移过程。其他的将会失败并重新启动多次。

# 选项 3:在触发部署之前，在 CD 管道中运行迁移

如今，大多数开发人员使用某种 CI/CD 管道来构建他们的服务，并将它们部署到 Kubernetes 集群中。考虑到这一点，也可以在 Kubernetes 部署之前，将迁移工具作为连续部署管道中的一个步骤来运行。

这是一个可行的解决方案，但是，由于数据库只能从 Kubernetes 集群内部访问，所以可能无法实现。这也意味着 CD 管道需要访问数据库机密，因此它们可能必须保存在两个位置，而不是一个中心位置(取决于 CI/CD 解决方案)。

# 选项 4:作为 Kubernetes 的工作运行数据库迁移

在选项 2 中，我们介绍了作为在 pod 启动前运行迁移的解决方案的 initContainers。除了使用 initContainers，我们还可以使用 Kubernetes 作业来达到同样的目的。然而，这带来了一点复杂性，因为 Kubernetes 没有提供在启动 pods 之前等待任务执行的原生功能。

为了克服这个问题，可以使用[服务](https://github.com/groundnuty/k8s-wait-for)作为 initContainer，不断检查迁移工作是否成功完成。只有在作业完成之后，initContainer 才会成功地完成它的执行，并使 pod 能够启动。

**注意**:对于这个用例，正在运行的 initContainers 需要访问权限来读取迁移作业的状态。

设置 initContainers 和 k8 作业的教程可以在本文中找到[:](/@jonastm/database-migration-with-a-kubernetes-job-and-initcontainers-d196dec8ac96)

[](/@jonastm/database-migration-with-a-kubernetes-job-and-initcontainers-d196dec8ac96) [## 使用 Kubernetes 作业和 initContainers 进行数据库迁移

### 本文展示了如何使用 Kubernetes 作业和 initContainers 进行数据库迁移。YAML 配置示例如下…

medium.com](/@jonastm/database-migration-with-a-kubernetes-job-and-initcontainers-d196dec8ac96) 

*如果你在 Kubernetes 上使用技术，可能会有更简单的解决方案，例如 Helm，在完全没有 initContainers 的情况下，使用*[*Helm Hooks*](https://itnext.io/database-migrations-on-kubernetes-using-helm-hooks-fb80c0d97805)*也可以实现同样的效果。*

# 其他提及

还有一些其他选项可能值得一看，但不常用:

*   [***Schema hero***](https://schemahero.io/):这使您能够将数据库模式定义为 YAML 的 Kubernetes 配置。当您更改配置时，会为您处理迁移。目前，不提供数据迁移(仅模式)。
*   [***迁移-操作符***](https://github.com/coderanger/migrations-operator) : Kubernetes 操作符，与选项 4 做同样的事情，但是以一种更自动化更容易配置的方式。没有企业支持。

# 结论:

一般来说，如果您的应用程序运行多个实例(pods ),我不推荐使用选项 1 或 2。在这些情况下，应首选**选项 3** 和 **4** ，以避免由于并行或长时间运行的迁移而导致的错误。一如既往，解决方案必须适合您的使用案例，这可能有其他先决条件和要求。

*你知道另一种选择吗？分享到评论区吧！*