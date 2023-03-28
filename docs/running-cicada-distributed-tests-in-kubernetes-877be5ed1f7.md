# 在 Kubernetes 上运行蝉分布式测试

> 原文：<https://medium.com/codex/running-cicada-distributed-tests-in-kubernetes-877be5ed1f7?source=collection_archive---------27----------------------->

## 如何让您的蝉测试在本地 k3d 集群中运行

![](img/8ecf7092729c0652e3899e832092e4f1.png)

由 [Lars Kienle](https://unsplash.com/@larskienle?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

[蝉分布式的](https://cicadatesting.github.io/cicada-distributed-docs/)可伸缩性和灵活性来自于能够运行容器化的测试工作负载。在发布的最初版本中，这仅在 Docker 中可用。蝉分布式 1.2.0 引入了 Kubernetes 支持，以在 K8s 集群中运行相同的工作负载。在本教程中，您将使用 [k3d](https://k3d.io/) (在 Docker 容器中运行的 Kubernetes 的一个轻量级版本)设置一个本地 K8s 集群，使用 [Kustomize](https://kustomize.io/) 修改用于本地使用的蝉分布式集群，并使用它来托管测试。

该指南也可在[蝉发布网站](https://cicadatesting.github.io/cicada-distributed-docs/docs/guides/kubernetes)上获得。

# 设置集群

从[安装 k3d](https://k3d.io/#installation) 开始，确保 [Kustomize](https://kubectl.docs.kubernetes.io/installation/kustomize/) 已安装并可与`kubectl -k`一起使用。安装完成后，启动 k3d 集群:

```
k3d cluster create -p "8283:30083@server[0]" -p "8284:30084@server[0]"
```

这将创建一个集群，在`localhost:8283`和`localhost:8284`上有两个节点端口。因为这些端口分别映射到集群中的`30083`和`30084`，我们还必须使用 Kustomize 修改蝉的安装。

首先，为覆盖创建一个目录，并将安装 YAML 放入一个文件中:

```
mkdir cicada-distributed-overlaycicada-distributed start-cluster --mode=KUBE > cicada-distributed-overlay/cicada.yaml
```

接下来，创建一个名为`cicada-distributed-overlay/patch.yaml`的文件，并将其添加到内容中:

这将覆盖`datastore-client`和`container-service`服务，使用绑定到集群中`30083`和`30084`的`NodePort`，因此我们可以在本地访问它们。

接下来，您需要使用 Kustomize 合并文件。为此，添加一个名为`cicada-distributed-overlay/kustomization.yaml`的文件:

接下来，通过使用`kubectl`中的`-k`标志来应用目录:

```
kubectl apply -k cicada-distributed-overlay
```

这将创建所有的资源并修改服务以便在`k3d`中使用。

# 将 API 引入 K8s

在上一篇文章中，开发了一个简单的 REST API 来演示蝉的分布式。该 API 的源代码可在[这里](https://github.com/cicadatesting/cicada-distributed-demos/tree/main/rest-api/app)获得。对于这个例子，你需要将 API 映像添加到 k3d 集群，并使用提供的 [Kube YAML](https://github.com/cicadatesting/cicada-distributed-demos/blob/main/rest-api/app/kube-app.yaml) 启动它。

演示应用程序可以从`cicadatesting/cicada-distributed-demos`克隆:

```
git clone [https://github.com/cicadatesting/cicada-distributed-demos.git](https://github.com/cicadatesting/cicada-distributed-demos.git)
```

首先，构建 API 和数据库，并将映像添加到集群:

`cicada-distributed-demos/rest-api/app`:

```
docker build -t cicadatesting/demo-api-app:local .docker build -t cicadatesting/demo-api-flyway:local -f flyway.dockerfile .k3d image import cicadatesting/demo-api-app:localk3d image import cicadatesting/demo-api-flyway:local
```

接下来，用`kube-app.yaml`中的代码安装 app:

```
kubectl apply -f kube-app.yaml
```

这将启动 API、数据库和安装数据库模式的作业。

# 运行测试

一旦一个示例应用程序运行，我们就可以对它运行蝉测试。导航至`cicada-distributed-demos/rest-api/integration-tests`。因为它运行在 k3d 中，所以需要将映像导入到集群中。要构建，请运行:

```
docker build -t cicadatesting/cicada-distributed-demo-integration-test:local .
```

接下来，使用以下内容导入图像:

```
k3d image import cicadatesting/cicada-distributed-demo-integration-test:local
```

最后，运行以下命令开始测试:

```
cicada-distributed run --mode=KUBE --image=cicadatesting/cicada-distributed-demo-integration-test:local
```

您应该看到测试开始运行，并执行 4 个测试场景。