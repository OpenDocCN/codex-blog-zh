# 使用 YAML 配置在 Kubernetes 上轻松部署 SonarQube

> 原文：<https://medium.com/codex/easy-deploy-sonarqube-on-kubernetes-with-yaml-configuration-27f5adc8de90?source=collection_archive---------1----------------------->

![](img/f6d88834faeb1f33e2dd6d19f6c7f99a.png)

使用 [Canva](https://www.canva.com/) 创建

K ubernetes 简化了我们开发的应用程序的部署和扩展。不仅是 it，我们用来开发应用程序的工具如果已经部署在 Kubernetes 上，也可以更容易维护。

这次我们将在 Kubernetes 上部署 SonarQube，这些工具主要用于检查我们的代码质量分数和代码漏洞。如果你在 Kubernetes 上直接查看 SonarQube 关于[部署方法的官方文档，他们会指导我们使用舵图作为部署方法。但是，如果你以前从未使用过舵图，或者你所在的地方有一些情况使你无法使用舵图，这可能是一个考虑因素。](https://docs.sonarqube.org/latest/setup/sonarqube-on-kubernetes/)

没关系，因为我们有一个替代解决方案来部署 YAML 配置，在此过程中创建了几个 YAML，我将在下面的部分进一步解释

# 一些要求

我在本文中使用的 Kubernetes 版本是`1.18.17-gke.1901`。如果您使用的版本比这个版本新或旧，只需修改配置以匹配您现在使用的版本。我将使 YAML 配置尽可能通用。

对于计算资源需求，我们至少需要与网站上写的系统需求[一样多的资源。我将使用的 SonarQube 图像来自数据库的官方 docker](https://docs.sonarqube.org/latest/requirements/requirements/) 和来自 Bitnami 的 PostgreSQL 图像。如果您使用另一个图像源，只需调整图像参数和驱动器安装位置。

> 你可以在[这个库](https://github.com/doctor500/sonarqube-on-kubernetes)中获得所有的代码片段，只需检查一下。

# 部署数据库(PostgreSQL)

为 PostgreSQL 创建持久卷声明和部署的配置。如果您使用的是外部服务，如完全托管的数据库服务等，您可以跳过这一步。

这是我的 PostgreSQL 部署的 YAML 配置，不要忘记在配置映射部分设置 PostgreSQL 凭证，我们稍后将在 Sonar YAML 上使用它。

# 部署应用程序

让我们从创建 SonarQube 使用持久卷声明的配置开始

现在我们将为 SonarQube 创建一个部署配置。在这个阶段，我们需要在配置 SonarQube 时做一点小技巧。在 Linux 环境中运行[sonar cube 所需的配置之一是通过将`max_map_count`的值更改为`524288`来增加虚拟内存的最大数量。如果 SonarQube 部署在一个 VM 上，这可能没什么大不了的，因为我们可以永久地改变它。但是在 Kubernetes 中，需要一点额外的步骤，以便在创建 pod 时可以继续应用这种配置。我们可以通过在部署模板中添加一个步骤 initContainer 来实现这一点。在这一步，我们将部署 busybox 并运行`max_map_count`配置。我使用值`262144`作为运行 SonarQube 所需的最小虚拟内存。](https://docs.sonarqube.org/latest/requirements/requirements/)

我们还需要注意的配置是数据库连接设置。使用 JDBC URL 格式设置数据库连接的官方 SonarQube 图像参数。你可以看到下面的例子

```
“jdbc:postgresql://postgres:5432/sonar_db”
```

*   `postgres`值基于 PostgreSQL 容器名在 Kubernetes 环境中，我们可以通过使用容器名来访问容器。将其更改为您定义的容器名称。
*   `sonar_db`值基于我们在 Postgres 配置图中定义的`POSTGRESQL_DATABASE`

# 部署入口

应用部署配置后，创建一个入口来公开服务。在这一步，我将使用 Nginx 控制器的入口。部署 Nginx 控制器并不太困难，因为您可以按照他们的文档中的步骤[进行操作。让我们从为 SSL 证书添加一个秘密开始，您将需要 SSL 密钥和 SLL 证书。](https://kubernetes.github.io/ingress-nginx/deploy/)

> 如果您想在 HTTP 协议中公开 SonarQube，则不需要这一步。您可以只移除入口 YAML 中的`tls`部分并应用它。

```
kubectl create secret tls my-fancy-certs — key ssl.key — cert ssl.crt -n sonar
```

创建秘密后，用 Nginx 类配置 ingress

我们开始吧。只要做一些快速设置，你的声纳就可以使用了。

# 关闭

通过在 Kubernetes 环境中安装 SonarQube，我们可以轻松地维护我们正在使用的版本。只需更改部署规范上的图像标签，然后 ***瞧吧**** ！

> *请务必阅读[版本升级说明](https://docs.sonarqube.org/latest/setup/upgrade-notes/)以防止不必要的事情发生

另一个需要注意的是，如果你是在`Java 15`环境下编译的应用程序，确保你使用的 Jacoco 至少是版本`0.8.7`(如果你使用 Jacoco 进行代码覆盖报告)，因为版本低于`0.8.7`的 Jacoco 还不支持 Java 15。

在 Gradle 中，您可以添加以下代码行来定义 Jacoco 版本:

```
jacoco { toolVersion = “0.8.7”}
```