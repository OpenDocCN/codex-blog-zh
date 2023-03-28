# 使用 GitHub 动作和种类测试 Kubernetes 操作符

> 原文：<https://medium.com/codex/testing-kubernetes-operators-using-github-actions-and-kind-c4086d37dd30?source=collection_archive---------7----------------------->

![](img/23f9a379f8f47eebbcb9ada9cd38f78d.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上[科学高清](https://unsplash.com/@scienceinhd?utm_source=medium&utm_medium=referral)拍摄的照片

自动化测试是任何管理良好的存储库的重要特征。它为每个人提供验证，以确保代码质量和功能。

在本文中，我们将涵盖林挺、单元测试、集成测试和 docker 构建。我们还将介绍一个在 Github 中管理秘密的好策略。

# 设置

在目录的根目录下，创建以下目录结构和文件。

```
.github/
  |- workflows/
    |- linting.yaml
    |- testing.yaml
    |- docker-build.yaml
Makefile
```

[GitHub 动作](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions#about-yaml-syntax-for-workflows)通过读取`.github`目录的内容来工作。

# 林挺

代码林挺是静态代码分析，用于标记编程错误、bug、风格错误和可疑结构。

在`linting.yaml`中增加以下内容。这将在您创建或更新拉取请求时运行工作流。如果它发现任何林挺错误，它会在您的 PR 上留下代码注释供您解决。

*   [Golang CI Lint Github 动作](https://github.com/golangci/golangci-lint-action)
*   [golangci-lint 工具](https://github.com/golangci/golangci-lint)

# 单元测试

我们将在下一个工作流程中实现单元测试和集成测试工作。首先，是单元测试。

在`testing.yaml`中，添加以下内容。这将在您创建或更新拉取请求时运行工作流。

首先，我们安装特定版本的 Golang，因为我们不能确定默认运行程序 ubuntu-latest 将使用什么版本。最后，我们使用 Makefile *运行我们的单元测试。*下面是 Makefile 的一个例子。

```
# Run tests
unit-test: fmt vet 
  go test ./pkg/... -coverprofile cover.out integration-test: fmt vet 
  go test ./controllers/... -coverprofile cover.out# Run go fmt against code
fmt: 
  go fmt ./... # Run go vet against code
vet: 
  go vet ./...
```

您可以调整`go unit-test`将要瞄准的路径。在这个例子中，它在`pkg`目录中寻找`*_test.go`文件。进行额外的验证。`go fmt`和`go vet`也在运行。

# 集成测试

接下来是集成测试。集成测试很可能需要与需要认证的系统进行交互。我们将添加 secrets 作为环境变量来满足这一需求。

修改`testing.yaml`，在底部添加一个新任务。下面是完整文件的样子。注意在*任务下增加了*集成测试*。*

首先，我们启动一个[类](https://kind.sigs.k8s.io/)集群来运行我们的操作符。我们正在测试 Kubernetes 版本 1.20.7。当它开始运行时，它会自动在 GitHub Action Runner 中设置 Operator-SDK 将用来运行的 *kube 配置*。

> Kind 是一个使用 Docker 容器“节点”运行本地 Kubernetes 集群的工具。Kind 主要是为测试 Kubernetes 本身而设计的，但也可以用于本地开发或 CI。

接下来，我们从 Azure Key Vault 中安全地获取我们的运营商需要的任何秘密。你可以在这里阅读更多关于*Azure/get-key vault-secrets*Github Action[。稍后，我们将讨论为什么我们会以这种方式获得秘密。](https://docs.microsoft.com/en-us/azure/developer/github/github-key-vault)

接下来，我们安装我们正在使用的 Operator-SDK 版本。

最后，我们通过调用`make integration-test`来运行集成测试

# 码头工人建造

我们将创建的最后一个工作流是为了测试我们是否能够成功地构建我们的操作员的容器映像。将以下内容添加到`docker-build.yaml`。

# 高效管理 GitHub 机密

你可以给 Github 添加秘密，并使用`${{ secrets.MY_SECRET_NAME }}`在动作中调用它们。这种方法很好，也很简单，但是在管理多个秘密时，这就变得令人头疼了。如果你有一个服务主体，它需要定期进行秘密轮换，你必须记住在任何地方都要改变它，包括 GitHub。此外，存储机密的位置越多，黑客的攻击面就越大。

然而，如果您从 Azure Key Vault 动态检索秘密值，您只需要担心在一个地方管理秘密轮换和安全性。这就是为什么*Azure/get-key vault-secrets*这么牛逼的原因。

# 包扎

设置这些工作流程后，请确保在合并 PR 之前要求它们通过。您可以通过为 *main* 创建一个分支保护规则并选择“合并前要求通过状态检查”来在存储库设置中实现这一点。然后列出我们在上面创建的工作流的名称。

现在，您的基于 Operator-SDK 的应用程序已经准备好进行自动化测试了！如果您有任何问题或意见，请联系我们！

干杯。