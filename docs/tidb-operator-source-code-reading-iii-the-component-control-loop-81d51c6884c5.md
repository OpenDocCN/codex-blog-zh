# TiDB 操作符源代码阅读(三):组件控制循环

> 原文：<https://medium.com/codex/tidb-operator-source-code-reading-iii-the-component-control-loop-81d51c6884c5?source=collection_archive---------15----------------------->

![](img/0d5c7aff75029d590778b7dfb8da7de8.png)

**作者:** [陈一文](https://github.com/handlerww)(TiDB 运营委员，PingCAP 软件工程师)

**Transcreator:** [黄然](https://github.com/ran-huang)；编辑:汤姆·万德

> 本系列以前的文章:

*   [*TiDB 运算符源代码阅读(一):概述*](/geekculture/tidb-operator-source-code-reading-i-overview-cf1891f1fd90)
*   [*TiDB 运算符源代码阅读(二):运算符模式*](/geekculture/tidb-operator-source-code-reading-ii-operator-pattern-67ddaf90983a)

在我的上一篇文章中，我介绍了我们如何设计和实现`tidb-controller-manager`和控制器的内部逻辑，以及每个控制器如何接收和处理变化。这次，我将描述我们如何实现组件控制器。

`TidbCluster`控制器管理 TiDB 中主要组件的生命周期。我以`TidbCluster`为例介绍一下组件控制回路的设计。**您将了解在 TiDB 集群生命周期管理期间如何编排控制循环事件，以及这些事件如何管理资源。**

本文只是对过程和定义的一般性介绍。每个组件的具体应用将在下一篇文章中讨论。现在，让我们开始吧。

# 调用组件控制循环

在[控制器内部逻辑](https://pingcap.com/blog/tidb-operator-source-code-reading-2-operator-pattern#controllers-internal-logic)部分，我提到了`TidbCluster`控制器的`updateTidbCluster`功能，它位于`pkg/controller/tidbcluster/tidb_cluster_control.go`。作为 TiDB 组件生命周期管理的入口，该功能调用一系列生命周期管理功能:

*   `c.reclaimPolicyManager.Sync(tc)`
*   `c.orphanPodsCleaner.Clean(tc)`
*   `c.discoveryManager.Reconcile(tc)`
*   `c.ticdcMemberManager.Sync(tc)`
*   `c.tiflashMemberManager.Sync(tc)`
*   `c.pdMemberManager.Sync(tc)`
*   `c.tikvMemberManager.Sync(tc)`
*   `c.pumpMemberManager.Sync(tc)`
*   `c.tidbMemberManager.Sync(tc)`
*   `c.metaManager.Sync(tc)`
*   `c.pvcCleaner.Clean(tc)`
*   `c.pvcResizer.Resize(tc)`
*   `c.tidbClusterStatusManager.Sync(tc)`

这些功能分为两类:

*   **实现 TiDB 组件**的控制回路，如 PD、TiDB、TiKV、TiFlash、TiCDC、Pump 和 Discovery。
*   **TiDB 组件**使用的 Kubernetes 资源的管理和**其他组件**的生命周期管理，如维护 PV 的回收策略，清理孤儿 Pods，维护 Kubernetes 资源的元信息，清理和扩展 PVC，管理`TidbCluster`对象的状态。

# TiDB 组件的生命周期管理流程

TiDB 主要组件的控制循环代码位于目录`pkg/manager/member`和以`_member_manager.go`结尾的文件中，如`pd_member_manager.go`。这些文件引用实现缩放和升级特性的其他文件，例如`_scaler.go`和`_upgrader.go`。

从组件的`_member_manager.go`文件中，我们可以识别通用实现:

```
// Sync Service
if err := m.syncServiceForTidbCluster(tc); err != nil {
    return err
}// Sync Headless Service
if err := m.syncHeadlessServiceForTidbCluster(tc); err != nil {
    return err
}// Sync StatefulSet
return syncStatefulSetForTidbCluster(tc)func syncStatefulSetForTidbCluster(tc *v1alpha1.TidbCluster) error {
    if err := m.syncTidbClusterStatus(tc, oldSet); err != nil {
        klog.Errorf("failed to sync TidbCluster: [%s/%s]'s status, error: %v", ns, tcName, err)
    } if tc.Spec.Paused {
        klog.V(4).Infof("tidb cluster %s/%s is paused, skip syncing for statefulset", tc.GetNamespace(), tc.GetName())
        return nil
    } cm, err := m.syncConfigMap(tc, oldSet) newSet, err := getnewSetForTidbCluster(tc, cm) if err := m.scaler.Scale(tc, oldSet, newSet); err != nil {
        return err
    } if err := m.failover.Failover(tc); err != nil {
        return err
    } if err := m.upgrader.Upgrade(tc, oldSet, newSet); err != nil {
        return err
    } return UpdateStatefulSet(m.deps.StatefulSetControl, tc, newSet, oldSet)
}
```

上面的代码执行两个主要任务:

*   同步服务:为 TiDB 组件创建或同步服务资源。
*   同步状态集:

同步组件状态。

检查`TidbCluster`是否停止同步。

同步配置映射。

根据`TidbCluster`中的配置生成新的 StatefulSet，并对新的 StatefulSet 执行相关操作，如滚动更新、向外扩展、向内扩展和故障转移。

创建或更新 StatefulSet。

组件控制循环重复执行上述任务，以确保组件保持最新。

以下各节介绍控制回路中完成的具体工作。

## 同步服务

当组件协调开始时，服务协调也开始。**该过程创建并同步组件**使用的服务，例如`cluster1-pd`和`cluster1-pd-peer`。

控制循环函数调用`getNewServiceForTidbCluster`函数，该函数根据`TidbCluster`自定义资源(CR)中记录的信息创建一个新的服务模板。如果服务不存在，控制循环函数创建一个服务；如果服务存在，它将新旧服务规范进行比较，并确定是否更新服务对象。

既有服务，也有允许其他人访问组件的无头服务。

*   如果组件不需要知道它正在与哪个实例对话，并且如果组件支持负载平衡，比如当 TiKV 和 TiDB 访问布局驱动程序(PD)时，组件使用服务。
*   如果组件需要知道哪个 Pod 正在提供服务，它使用一个无头服务通过 [Pod DNS](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#stable-network-id) 进行通信。例如，当 TiKV 启动时，它公开其 Pod DNS 作为广告地址，以便其他 Pod 可以通过 Pod DNS 访问 TiKV。

## 同步状态集

服务同步后，组件将连接到集群网络，因此它们可以访问集群，也可以从集群内部被访问。控制循环进入`syncStatefulSetForTidbCluster`功能，并开始协调状态集。

协调 StatefulSet 的第一步是通过运行`syncTidbClusterStatus`函数来同步组件状态。然后，根据状态信息，将执行其他操作，如升级、向内扩展、向外扩展和故障转移。

**同步组件状态**

TiDB 操作员的关键操作之一是同步组件状态。状态信息包含:

*   Kubernetes 的组件信息，包括集群中副本的数量、更新状态和映像版本。TiDB 操作符还检查 StatefulSet 是否在更新过程中。
*   TiDB 的内部集群信息。TiDB 操作员同步来自 PD 的信息，包括关于 PD 成员、TiKV 存储和 TiDB 成员的信息。TiDB 操作员还对 TiDB 执行运行状况检查。

**检查 TiDB 集群是否暂停同步**

同步状态后，TiDB 操作员通过检查`tc.Spec.Paused`来确定集群是否停止同步。如果同步暂停，TiDB 运算符将跳过更新 StatefulSet 的所有后续操作。

**同步配置图**

同步状态后，`syncConfigMap`函数更新 ConfigMap，它包含组件的配置文件和启动脚本。

配置文件是从 YAML 文件的`Spec`中的`Config`项提取的。TiDB 运营商支持直接使用 TOML 配置(推荐)或从 YAML 转换配置。

启动脚本包含组件所需的启动参数，并使用这些参数启动组件进程。

当组件需要从 TiDB 操作符获得启动参数时，信息处理在发现组件中执行。例如，当 PD 需要使用参数来确定它应该初始化一个节点还是加入一个节点时，它使用 wget 来访问 Discovery 并获取参数。通过从启动脚本中获取参数，TiDB Operator 避免了在 StatefulSet 被更新时出现意外的滚动更新。这可能会影响在线服务。

**生成新的 StatefulSet**

`getNewPDSetForTidbCluster`函数获取一个新的 StatefulSet 模板，它包含新生成的服务和 ConfigMap 的引用。该功能使用最新的状态和规格生成其他项目，如`env`、`container`和`volume`。

然后，这个新的 StatefulSet 经历三个过程:滚动更新、扩展和故障转移。最后，`UpdateStatefulSet`函数比较现有的 StatefulSet 和新的 stateful set，并决定是否更新现有的 stateful set。

*   滚动更新

`m.upgraded.Upgrade`函数执行与滚动更新相关的操作，主要更新 StatefulSet 中的`UpgradeStrategy.Type`和`UpgradeStrategy.Partition`。

滚动更新操作使用 StatefulSet 中的滚动更新策略来执行。当组件被协调时，它将 StatefulSet 的更新策略设置为滚动更新。在 Kubernetes 中，可以通过配置`UpgradeStrategy.Partition`来控制滚动更新进度。StatefulSet 仅更新之前未更新且 ID 大于或等于`UpgradeStrategy.Partition`值的 pod。TiDB 运营商使用这种机制来确保每个 Pod 仅在能够正常地向外部应用提供服务之后才被滚动更新。

当集群未被更新或正处于更新的开始阶段时，组件协调将`UpgradeStrategy.Partition`设置为状态集中最大的 Pod ID。这可以防止任何 Pod 被更新。更新开始后，当 Pod 更新并在重启后提供服务时，Pod 被视为成功升级。TiDB 操作员然后递减`UpgradeStrategy.Partition`值并更新下一个 Pod。

*   放大和缩小

`m.scaler.Scale`缩放组件。它的主要任务是更新 StatefulSet 中组件的`Replicas`。

`m.scaler.Scale`函数一个一个地缩放组件，一次一步。它将`TidbCluster` CR(例如`tc.Spec.PD.Replicas`)中某个组件的`Replicas`号与当前的`Replicas`号进行比较。在此基础上，它确定是向外扩展还是向内扩展组件，并在一个副本上执行扩展操作。然后进入下一轮组件协调。通过多轮对账，`m.scaler.Scale`完成所有缩放需求。

在缩放过程中，PD 需要转移 Leader，TiKV 需要删除门店。这些操作使用 PD APIs。对账时，`m.scaler.Scale`函数使用 PD APIs 执行操作，并检查操作是否成功。如果是，它将移动到下一个缩放操作。

*   故障切换

`m.failover.Failover`功能执行与灾难恢复相关的操作，包括发现故障、记录故障状态和从故障中恢复。

如果在部署 TiDB 操作员时启用`AutoFailover`，TiDB 操作员将监控组件故障状态。当发现一个时，TiDB 操作员将故障信息记录到`FailureStores`或`FailureMembers`。接下来，它启动一个新的组件 Pod 来接管故障 Pod 的工作负载。在发生故障的 Pod 恢复后，TiDB Operator 修改 StatefulSet 中的副本数量，以便在新的 Pod 中进行扩展。

当 TiDB Operator 为 TiKV 和 TiFlash 执行故障切换时，默认情况下不会扩展新创建的 Pod。您需要配置`spec.tikv.recoverFailover: true`来启用自动放大。

**更新现有的 StatefulSet**

在最后一个阶段，创建新的 StatefulSet。控制循环现在进入`UpdateStatefulSet`函数，该函数将新的 StatefulSet 与现有的 stateful set 进行比较。如果两个 StatefulSet 不一致，该函数将更新现有的 stateful set。

该函数还检查是否存在不受 TiDB 操作符管理的 StatefulSets。因为 TiDB Operator 的早期版本使用 Helm Chart 来部署 TiDB，所以 TiDB Operator 需要向这些旧的 StatefulSets 添加依赖标记，并将它们包含在生命周期管理中。

完成上述操作后，`TidbCluster` CR 的状态更新为最新版本。相关的服务和配置映射已创建。生成新的 StatefulSet，它执行滚动更新、伸缩和故障转移。组件协调继续进行，监控组件的生命周期，并对生命周期状态变化和用户指定的变化做出响应。整个集群运行正常。

## 其他生命周期管理任务

除了 TiDB 中主要组件的协调之外，其他生命周期管理操作由以下功能执行:

*   发现配置 PD 启动参数和 TiDB 仪表板代理。它为组件提供动态信息以供使用，从而避免修改可能导致 Pod 滚动更新的 ConfigMap。
*   回收策略管理器同步`tc.Spec.PVReclaimPolicy`的配置。默认情况下，PV 回收策略设置为`Retain`，以降低数据丢失的风险。
*   孤立单元清理程序在 PVC 创建失败时清理单元。然后 StatefulSet 控制器再次尝试创建 pod 和相应的 PVC。
*   PVC 清洁剂清除标记为可删除的 PVC。
*   PVC 尺寸调整器横向扩展。在云上运行 TiDB Operator 时，可以通过修改`TidbCluster`中的存储配置来改变 PVC 的大小。
*   Meta Manager 将`StoreIDLabel`、`MemberIDLabel`和`NamespaceLabel`同步到 pod、PVC 和 PV 的标签。
*   TiDBCluster 状态管理器同步与`TidbMonitor`和 TiDB 仪表板相关的信息。

# 摘要

至此，您已经了解了 TiDB 组件控制回路的设计，包括:

*   协调功能如何遵循其相应的过程来检查组件资源
*   协调功能如何将用户期望的状态转变为实际的组件状态

TiDB 操作符中几乎所有的控制循环都符合本文描述的设计逻辑。在以后的文章中，我将进一步解释如何将这个逻辑应用于每个组件来管理组件的生命周期。

如果您对 TiDB 运营商有任何问题或想法，欢迎[加入我们的 Slack 频道](https://slack.tidb.io/invite?team=tidb-community&channel=sig-k8s&ref=pingcap-blog)或在 [pingcap/tidb-operator](https://github.com/pingcap/tidb-operator) 参与我们的讨论！

*原载于 www.pingcap.com*[](https://en.pingcap.com/blog/tidb-operator-source-code-reading-3-component-control-loop)**2021 年 7 月 21 日**