# GCE 上的 Kubernetes 集群:超越 Kubeadm

> 原文：<https://medium.com/codex/kubernetes-cluster-on-gce-beyond-kubeadm-4a954935e2c8?source=collection_archive---------2----------------------->

# 为什么？

用一个简单的 kubectl apply 部署整个应用程序堆栈是一件令人愉快的事情。给定期望，当应用度量服务器[清单](https://github.com/kubernetes-sigs/metrics-server#installation) [没有开箱即用](https://github.com/kubernetes-sigs/metrics-server/issues/300)时，我感到失望。由默认 kubeadm 配置创建的 Kubernetes 集群使用 kubelet 服务器的自签名证书，这与 metrics-server 的要求不匹配。我们将讨论创建一个不依赖于度量服务器的 kube let-unsecure-TLS 标志的集群的步骤。

你可能想知道，当你已经有了 [GKE](https://cloud.google.com/kubernetes-engine) 的时候，为什么还要在 GCP 运行他们的 Kubernetes 集群。我有三个字送给你:*自定义节点图片*。在 GKE 上，你只有两个选择来创建你的 worker 节点映像，[容器优化的 OS 或者 Ubuntu](https://cloud.google.com/kubernetes-engine/docs/concepts/node-images) 。因此，您将无法使用 GCP 计算支持的许多功能，如[启动脚本](https://cloud.google.com/compute/docs/instances/startup-scripts)、[虚拟机管理器](https://cloud.google.com/compute/docs/vm-manager)或[嵌套虚拟机](https://cloud.google.com/compute/docs/instances/nested-virtualization/overview)。如果您想使用 coredns、自定义自动缩放器、外部认证机构或 [GKE 问题跟踪器](https://issuetracker.google.com/savedsearches/559746)上要求的任何其他功能，您也可能会有所欠缺。

# 什么？

我们将从定义集群的功能开始:

*   工作节点应该只有私有 IP
*   API 服务器应该使用所有 ETCD 成员
*   Kubelet 服务器和度量服务器证书应由群集 CA 签名
*   持久性卷声明应由 GCP 持久性磁盘支持
*   GCE 入口控制器应该能够创建 HTTP(S)负载平衡器

# 怎么会？

我们将使用 gcloud CLI 调配基础架构。如果您没有 gcloud CLI 设置，您可以遵循本指南。

上船啦

![](img/3dbcfb9b212e1d47482039d76c651631.png)

马雷克·鲁齐克绘画

## 供应基础设施

我们将从为 k8s 主服务器创建 VPC、计算节点和负载平衡器开始。接下来，我们需要创建一个 worker 节点，因为云控制器和入口控制器都不公开运行在主节点上的服务。最后，我们将设置一个云 NAT，以确保我们的实例可以在没有公共 IP 的情况下访问互联网。为了简单起见，这里我使用的是普通的 ubuntu-2004-lts 图像。当然，如果你愿意，你可以[使用你自己的自定义图像](https://cloud.google.com/compute/docs/instances/create-start-instance#creating_an_instance_from_a_custom_image)。

## 配置主节点

我们需要在所有节点上运行以下命令。我建议启用 tmux 的同步窗格。我们将配置 containerd 使用 systemd 作为它的 cgroup 驱动程序，否则 kubeadm 会抱怨没有遵循[的建议](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#cgroup-drivers)。

## 自举 ETCD 集群

使用[外部 etcd](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/ha-topology/#external-etcd-topology) 配置时，我们一般使用专用节点。这里，我们将在与 k8s 相同的主节点上设置我们的 etcd 集群。默认情况下，kubeadm 也在同一个节点上创建 etcd。唯一的区别是，默认 kubeadm 设置中的 API 服务器只使用一个 etcd 节点，并且没有 etcd 故障。我们的 API 服务器将使用所有三个 etcd 成员。
我们将需要配置 kubelet 暂时运行在独立模式下(不依赖于 API 服务器),以引导 etcd 集群。以下命令主要基于 [kubeadm 文档](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/setup-ha-etcd-with-kubeadm/)。

## 引导 Kubernetes 集群

不使用 GKE 并不意味着我们不能将 GCP 持久磁盘用于 Kubernetes StorageClass 或 LoadBalancer 服务类型。我们需要做的就是在 k8s 组件上配置云提供商。请注意，群集节点应该对 GCP 计算机具有管理员访问权限。默认情况下，GCP compute 使用的服务帐户对项目拥有编辑权限。所以很有可能你在这里已经被覆盖了。

我们将配置 Kubelet 服务器使用由我们的集群 CA 签署的证书。它将确保我们不必运行带有 kube let-unsecure-TLS 标志的 metrics-server。启用 serverTLSBootstrap 意味着您需要手动批准 kubelet 服务器的 CSR，或者设置一个类似于[橡皮图章](https://github.com/kontena/kubelet-rubber-stamp)的服务。希望，当我们内置了对[kube let 服务器证书引导](https://github.com/kubernetes/enhancements/issues/267#issuecomment-755765107)的支持时，这将变得更容易。

## 部署 GCE 入口控制器

根据我的经验，GCLB 部署文档不仅[令人困惑](https://github.com/kubernetes/ingress-gce/tree/master/docs/deploy/gce)，而且[已经过时](https://github.com/kubernetes/ingress-gce/pull/1498)。我真的怀疑让[容器本地负载平衡](https://cloud.google.com/kubernetes-engine/docs/how-to/container-native-load-balancing)在 GKE 之外工作的可能性。您仍然可以使用 GCP 的 HTTP(S)负载平衡器作为入口。只需确保添加到入口的服务是 NodePort 类型的。

## 部署度量服务器

除了依赖 kube let-unsecure-TLS 标志之外，metrics-server 还在其 APIService 中使用[insecureskiptlsverive](https://github.com/kubernetes-sigs/metrics-server/issues/544)。将 insecureSkipTLSVerify 设置为 true 意味着 API 服务器不会验证 metrics-server 证书。我们需要配置 metrics-server 来使用由我们的集群 CA 签署的证书，以避免使用不安全的 TLS。[jenting/secure-metrics-server](https://github.com/jenting/secure-metrics-server)存储库在记录流程方面做得非常出色。我在下面更新了它，以避免使用 [kustomize](https://github.com/kubernetes-sigs/kustomize) 。

## 测试

最后，我们将确保插件按预期工作。

# 结论

Kubeadm 在创建最小可行集群方面做得很好。不管怎样，当涉及到配置像 metrics-server 和 [GLBC](https://github.com/kubernetes/ingress-gce) 这样的插件时，你可能最终会在互联网上搜索。我希望在一个地方收集所有这些信息可以帮助其他人节省一些时间。如果你正在关注这篇文章，并且不能让一切正常工作，请随时留下你的评论。我很乐意帮忙。