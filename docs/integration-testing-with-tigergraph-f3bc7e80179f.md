# 使用 TigerGraph 进行集成测试

> 原文：<https://medium.com/codex/integration-testing-with-tigergraph-f3bc7e80179f?source=collection_archive---------7----------------------->

## [抄本](https://medium.com/codex)

## 支持 TigerGraph 的 Gradle 应用程序的 CI/CD 和 GitHub 操作。

![](img/e063211f8504a4f2bf80c67a8887d08d.png)

马特·迪恩通过[https://kiptoe.com/](https://kiptoe.com/)拍摄的照片

集成测试有助于我们验证我们的应用程序在与第三方软件交互时是否像在生产中一样正常工作。当集成测试包括一个数据库时，这就带来了它自己的一系列配置和设置。

Giraffle 是一个流行的 Gradle 插件，用于部署在 CI/CD 中应用的模式创建、加载作业和查询。已经有一些关于如何使用 Giraffle 进行部署的文章，但是在下面的文章中，我们将介绍一种在 GitHub 操作中使用本地化的 TigerGraph Docker 容器来运行集成测试的简单而免费的方法。

将涵盖哪些内容:

1.  通过 CLI 创建简单的 Gradle 项目
2.  为 TigerGraph 设置基本 Docker 编写脚本
3.  使用 Avast Docker 编写插件在梯度验证任务中启动 TigerGraph
4.  在测试开始运行之前，在启动时运行 GSQL 脚本
5.  使用 GitHub 操作运行集成测试，作为 CI/CD 的一部分

使用的 Docker 映像是本文中创建和讨论的[，而用于协调旋转容器的 Gradle 插件是](https://towardsdatascience.com/efficient-use-of-tigergraph-and-docker-5e7f9918bf53) [Avast 插件](https://github.com/avast/gradle-docker-compose-plugin)。

本指南的最终代码示例可以在我的 GitHub 上找到[。](https://github.com/DavidBakerEffendi/tigergraph-int-test-example)

# 创建一个简单的 Gradle 项目

在要创建项目的目录中，从命令行运行以下命令。在本例中，我们将使用 JDK 11 和 Gradle 6.7.1。

```
gradle init \
    --type java-application \
    --test-framework junit-jupiter \
    --dsl groovy
```

系统将提示您输入项目名称和源包——这两者都由您决定。

最终的项目将是一个简单的 Java 应用程序，JUnit 5 作为测试框架，Groovy 作为`build.gradle`的 DSL。在`src/main`和`src/test`下将分别生成一个名为`App`的类和一个名为`AppTest`的测试类。

# 在梯度任务中启动 TigerGraph

首先，让我们创建一个简单的 Docker 编写脚本，它将创建我们的 Docker 实例。使用的 TigerGraph 映像允许我们在启动时运行 GSQL 脚本。让我们把下面的脚本放在`src/test/resources/docker/tigergraph.yml`下面。

其次，让我们将 Avast 插件包含在我们的`build.gradle`中。在撰写本文时，最新版本是 0.14.0。这个插件将在我们的集成测试中运行我们的 TigerGraph Docker 编写脚本。

```
plugins {
    id "com.avast.gradle.docker-compose" version "0.14.0"
}
```

现在让我们创建一个验证任务，它将在我们的`build.gradle`中运行集成测试。在这个例子中，我们将通过在类名中包含`IntTest`来将 JUnit 单元测试与集成测试分开。

```
test {
    useJUnitPlatform()
    exclude "**/*IntTest*"
    testLogging { events "FAILED", "SKIPPED" }
}task intTest(type: Test) {
    useJUnitPlatform()
    description = "Execute integration tests."
    group = "verification"
    include "**/*IntTest*"
    testLogging **{** events "FAILED", "SKIPPED" **}** doFirst **{** dockerCompose.exposeAsEnvironment(intTest) **}** }
```

然后需要告诉 Avast 插件我们的 Docker 编写脚本在哪里，以及它应该与哪个 Gradle 任务相关联。

```
dockerCompose **{** tigerGraphSetup **{** useComposeFiles = ["src/test/resources/docker/tigergraph.yml"]
        isRequiredBy(project.tasks.intTest)
    **}
}**
```

此时，`./gradlew intTest`号将会运行

1.  通过在 Docker 编写脚本上执行`docker-compose`来设置我们的数据库
2.  运行类名中带有`IntTest`的所有测试
3.  拆除容器及其相关联的卷

# 测试前创建模式和安装查询

为了将我们的测试 TigerGraph 实例配置成与生产中的模式和查询相似，我们需要在执行测试之前运行 GSQL 脚本。

查询将按字母顺序执行，这就是为什么只需在脚本前面加上执行顺序就很方便。

## 创建我们的 GSQL 脚本

让我们从创建第一个 GSQL 脚本开始，并将其放在`src/test/resources/config/1-schema.gsql`下。我们将简单地使用 [GSQL 101](https://docs.tigergraph.com/start/gsql-101) 中的模式和查询。

然后，我们可以为我们的查询创建一个单独的脚本，并将其命名为`src/test/resources/config/2-queries.gsql`。

## 启用运行状况检查

`status`查询是任意的，但应该是我们的最后一个查询，因为我们的 Docker 健康检查将使用它来指示所有查询何时已经成功安装。这是因为 TigerGraph 查询是按顺序安装的。

Avast 插件将只在容器健康时运行测试。让我们调整 Docker 编写脚本，使其包含健康检查，并将我们的脚本绑定到`/docker-entrypoint-initdb.d`。

现在运行`./gradle intTest`将另外建立一个图表模式，并在运行我们的集成测试之前安装查询。这可以进一步扩展，以包括和运行加载作业。

# 创建集成测试

让我们创建一个可以在集成测试期间运行的测试类，它将调用我们的 TigerGraph 实例。Java 11 为我们提供了一些不错的工具来发出 HTTP 请求，这就是我们与数据库通信的方式。

## 加载一些测试数据

我们的数据库需要一些虚拟数据来测试。我们可以创建一个简单的 JSON 有效负载，通过 TigerGraph 的[内置端点](https://docs.tigergraph.com/dev/restpp-api/built-in-endpoints)插入数据。用以下内容创建一个文件`src/test/resources/data/payload.json`。

这对于这样一个小例子来说是合适的，但是对于更大的数据集，加载测试数据的另一种方式是通过一个接收 CSV 文件的加载作业。

## 设置我们的测试环境

现在在`AppTest`旁边创建一个名为`TigerAppIntTest`的测试类。提醒一下，我们将利用`IntTest`在类名中的事实来区分这是一个集成测试。

我们将通过在一个`@BeforeAll`方法期间将我们的测试数据插入数据库开始，并在一个`@AfterAll`方法期间在我们的测试结束时清除数据库。

## 测试查询

现在我们的测试环境已经设置好了，我们可以测试`hello`查询了。下面的测试可以做到这一点。

一旦建立了数据库，现在运行`./gradlew intTest`将按以下顺序运行方法:`setUpAll`、`helloQueryTest`和`tearDownAll`。

# 使用 GitHub 操作运行 TigerGraph 集成测试

使用我的`dbakereffendi/tigergraph`映像的一个优点是，考虑到 CI/CD 服务的资源限制，它们很小。也就是说，从下载映像到安装模式和查询，集成测试阶段仍然非常耗时。

要开始在 GitHub Actions 上运行我们的测试，请在您的项目`.github/workflows/ci.yml`的根目录下创建以下文件。

当您向`main`分支推送或者有一个拉取请求时，这将运行单元和集成测试。根据您选择的分支名称、操作系统或 Java 版本调整您的配置，但这里重要的是 Gradle 命令。

# 结论

通过使用 Docker、GitHub Actions 和 Gradle，我们已经成功地用一个本地化的 TigerGraph 实例免费运行了一个 TigerGraph 支持的应用程序的集成测试。

这可以扩展到运行加载作业，使用 Giraffle 部署到生产环境，或者在测试期间使用 Avast 插件运行多个第三方 Docker 服务。

提醒一下，本文中讨论的所有内容的完整工作示例可以在[我的 GitHub 库](https://github.com/DavidBakerEffendi/tigergraph-int-test-example)中找到。