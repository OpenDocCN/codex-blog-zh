# OKE 上的 YugabyteDB(Oracle Cloud Kubernetes)

> 原文：<https://medium.com/codex/yugabytedb-on-oke-oracle-cloud-kubernetes-265741b6b19a?source=collection_archive---------4----------------------->

![](img/230885f3c3b14c8d0fc0e1202f7b0d92.png)

[寺鹂](https://unsplash.com/@templecerulean?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我们已经记录了如何在主要的云提供商(【https://docs.yugabyte.com/latest/deploy/public-clouds】)上安装 YugabyteDB。当我加入 Yugabyte 时，许多来自 oracle 社区的朋友问我关于 Oracle 云(OCI)的问题。

在 Oracle 云中，有两种方式运行容器:

*   用 IaaS(基础设施即服务)自己动手显然，你可以调配一些运行 Docker 的计算实例(以及网络和存储)并安装 Kubernetes(或其他编排)。Oracle 为此提供了一个平台安装程序:[https://github.com/oracle/terraform-kubernetes-installer](https://github.com/oracle/terraform-kubernetes-installer)。
*   管理与 OKE (OCI 容器引擎为 Kubernetes)快速部署 Kubernetes，升级容易…注册表(OCIR)和 Kubernetes 引擎(OKE)是免费的。我们只为资源(计算实例、存储、负载平衡器)付费。

# 开发人员服务控制台

我在这里用管理的，OKE。Kubernetes 服务可以在 OCI 菜单的开发者服务下找到。然后在容器和工件中点击 Kubernetes 集群(OKE)。

![](img/4b024573ef6df3ca4b6c743ecbfc85aa.png)

您也可以通过[https://cloud.oracle.com/containers/clusters](https://cloud.oracle.com/containers/clusters)并选择您所在的地区来直接访问它。在 Oracle Cloud 中，价格不取决于地区，因此与用户的延迟是主要标准。这也可以是在您的数据中心运行托管服务的 Cloud@Customer。

在这里你可以看到你的 K8s 星团。

![](img/c2b76b9f8acdc43c61085c32ce595f18.png)

我将在这里选择快速创建向导。

![](img/b80d62b95c29344cad28e9eba4a5e592.png)

要提供的主要信息是:

*   集群的名称
*   要在主节点上运行的 Kubernetes 的版本

此设置将创建:

*   带有 NAT、互联网和服务网关的 VCN
*   3 个子网(一个专用于 worker 节点，另一个是公共的，因为在本演示中我保留了默认的“公共 worker ”)
*   2 个负载平衡器(用于主服务器和 t 服务器)，以及它们的安全列表
*   工作节点和 API 端点

我在这里使用公共端点，因为这是一个实验室，但显然你把你的数据库放在一个私有子网上。

![](img/da966c59381b10ca0f2aee1bcaeb03ab.png)

将创建第一个节点池，节点数在此处定义。我为该池中的每个节点定义了一个 4 OCPU (= 4 个英特尔酷睿= 8 个操作系统线程)形状，通过 Flex 形状，您可以独立调整 CPU 和 RAM 的大小。不要忘记，数据库将存储在每个节点上，有两个挂载的块卷。在高级选项中，如果您想在没有 kubectl 的情况下访问节点，可以上传您的公共 ssh 密钥。

![](img/4e8f2ac6e22c0476e6a000fe1b626f13.png)

您可以直接查看和创建它，或者“另存为堆栈”以便稍后使用 Terraform 创建它:

![](img/16751885d98e02ed9c2dd0216db343a0.png)

您还可以查看“支持”选项卡，您可以通过浮动在屏幕右侧的新环形浮标图标访问该选项卡。

下面是正在创建的服务(以及它们的 OCID，Oracle Cloud 中的标识符):

![](img/50f56db328151a391d873fc8448aef17.png)

有了它，我有 3 个节点在运行，每个可用性域(相当于 OCI 的可用性区域)一个，我可以在“计算”服务中看到，还有我的其他虚拟机:

![](img/275c4042f47e5e80f29db3548bd21f57.png)

这是将要计费的内容。如果您想要停止节点，可以将池缩小到零个节点。

当然，当您停止所有节点时，账单中仍然会有数据块卷和负载平衡器。

# Kubeconfig

现在，安装 YugabyteDB 最简单的方法是从云 Shell 继续使用 kubectl，并让设置配置的所有信息在 Access Cluster 按钮上可见。这就建立了 KUBECONFIG。

![](img/2dc371745126cb441869d4d8652ed880.png)

以下是命令(当然，您不应该复制/粘贴下面的命令，而是从快速启动/访问群集复制您的群集的详细信息):

```
dev@cloudshell:~ (uk-london-1)$ **umask 066**
dev@cloudshell:~ (uk-london-1)$ **oci ce cluster create-kubeconfig --cluster-id ocid1.cluster.oc1.uk-london-1.aaaaaaaadlkettlvl4frdlz6m7j6oiqvcupse4z6qzbdtxw74cbz655xbyfq --file $HOME/.kube/config --region uk-london-1 --token-version 2.0.0  --kube-endpoint PUBLIC_ENDPOINT**
```

我已经把 umask 设置成只有我一个人可以阅读。你也可以事后再做。

```
dev@cloudshell:~ (uk-london-1)$ **chmod 600 $HOME/.kube/config**
```

检查版本:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl version**
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.10", GitCommit:"a84e568eeb56c4e3966314fc2d58374febd12ed7", GitTreeState:"clean", BuildDate:"2021-03-09T14:37:57Z", GoVersion:"go1.13.15 BoringCrypto", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.12", GitCommit:"396aaa6314665118cf108193f0cc2318f7112979", GitTreeState:"clean", BuildDate:"2021-06-30T16:15:03Z", GoVersion:"go1.15.13 BoringCrypto", Compiler:"gc", Platform:"linux/amd64"}
```

头盔已经安装:

```
dev@cloudshell:~ (uk-london-1)$ **helm version**
version.BuildInfo{Version:"v3.3.4", GitCommit:"82f031c2db91441a56f7b05640a3dbe211222e81", GitTreeState:"clean", GoVersion:"go1.15.10"}
```

# 安装 YugabyteDB

我准备从 helm charts 创建一个 yugabytdb 集群，这些都有文档记录:[https://docs . yugabyte . com/latest/deploy/kubernetes/single-zone/OSS/helm-chart/](https://docs.yugabyte.com/latest/deploy/kubernetes/single-zone/oss/helm-chart/)将 yugabytdb 图表添加到存储库中:

```
dev@cloudshell:~ (uk-london-1)$ **helm repo add yugabytedb** [**https://charts.yugabyte.com**](https://charts.yugabyte.com)
"yugabytedb" has been added to your repositories
```

更新它:

```
dev@cloudshell:~ (uk-london-1)$ **helm repo update**
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "yugabytedb" chart repository
Update Complete. ⎈Happy Helming!⎈
```

检查 YugabyteDB 版本:

```
dev@cloudshell:~ (uk-london-1)$ **helm search repo yugabytedb/yugabyte**
NAME                    CHART VERSION   APP VERSION     DESCRIPTION
yugabytedb/yugabyte     2.7.1           2.7.1.1-b1      YugabyteDB is the high-performance distributed ...
```

准备安装在其专用命名空间中:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl create namespace yb-demo** 
namespace/yb-demo createddev@cloudshell:~ (uk-london-1)$ **helm install yb-demo yugabytedb/yugabyte --namespace yb-demo --wait --set gflags.tserver.ysql_enable_auth=true,storage.master.storageClass=oci-bv,storage.tserver.storageClass=oci-bv**
```

我启用了身份验证(默认的“yugabyte”用户密码是“yugabyte”)。在服务器标志' ysql_enable_auth '设置为 true 的情况下启用身份验证。

# 关于存储类的说明

请注意，在我的第一次尝试中，没有提到 StorageClass，我得到的结果是这样的，因为默认值不正确:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl describe pod --namespace yb-demo**
…
Events:
  Type     Reason            Age    From               Message
  ----     ------            ----   ----               -------
  Warning  FailedScheduling  6m44s  default-scheduler  no nodes available to schedule pods
  Warning  FailedScheduling  6m43s  default-scheduler  no nodes available to schedule pods
  Warning  FailedScheduling  6m39s  default-scheduler  0/1 nodes are available: 1 pod has unbound immediate PersistentVolumeClaims.
  Warning  FailedScheduling  6m18s  default-scheduler  0/3 nodes are available: 3 pod has unbound immediate PersistentVolumeClaims.
```

未绑定 PersistentVolumeClaims 的原因是:

```
Events:
  Type     Reason              Age                 From                         Message
  ----     ------              ----                ----                         -------
  Warning  ProvisioningFailed  4m (x142 over 39m)  persistentvolume-controller  storageclass.storage.k8s.io "standard" not found
```

让我们检查一下存储类:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl get storageclass**
NAME            PROVISIONER                       RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
oci (default)   oracle.com/oci                    Delete          Immediate              false                  3h32m
oci-bv          blockvolume.csi.oraclecloud.com   Delete          WaitForFirstConsumer   false                  3h32m
```

这就是我提到“oci-bv”(块卷的容器存储接口)存储类的原因:`--set storage.master.storageClass=oci-bv,storage.tserver.storageClass=oci-bv`

# 检查安装

安装需要几分钟，以下是启动并运行的 pod:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl --namespace yb-demo get pods**
NAME       READY   STATUS RESTARTS   AGE
yb-master-0 1/1 Running   0      100s
yb-master-1 1/1 Running   0      100s
yb-master-2 1/1 Running   0      100s
yb-tserver-0   1/1 Running   0      100s
yb-tserver-1   1/1 Running   0      100s
yb-tserver-2   1/1 Running   0      100s
```

kubectl exec 允许您在'【T1]'之后执行一个 shell 命令。我可以这样检查节点的文件系统、内存和 cpu:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl exec --namespace yb-demo -it yb-tserver-0 -- df -Th /mnt/disk0 /mnt/disk1**
Defaulting container name to yb-tserver.
Use 'kubectl describe pod/yb-tserver-0 -n yb-demo' to see all of the containers in this pod.
Filesystem Type  Size  Used Avail Use% Mounted on
/dev/sdd   ext4   50G   77M   47G   1% **/mnt/disk0**
/dev/sde   ext4   50G   59M   47G   1% **/mnt/disk1**
dev@cloudshell:~ (uk-london-1)$ **kubectl exec --namespace yb-demo -it yb-tserver-0 -- free -h**
Defaulting container name to yb-tserver.
Use 'kubectl describe pod/yb-tserver-0 -n yb-demo' to see all of the containers in this pod.
total    used    free  shared  buff/cache   available
Mem:        **30G**    1.1G     23G     36M    6.5G     29G
Swap:        0B      0B      0B
dev@cloudshell:~ (uk-london-1)$ kubectl **exec --namespace yb-demo -it yb-tserver-0 -- lscpu**
Defaulting container name to yb-tserver.
Use 'kubectl describe pod/yb-tserver-0 -n yb-demo' to see all of the containers in this pod.
Architecture:      x86_64
CPU op-mode(s):    32-bit, 64-bit
Byte Order:        Little Endian
CPU(s):            8
On-line CPU(s) list:   0-7
Thread(s) per core: 2
**Core(s) per socket: 4**
**Socket(s):         1**
NUMA node(s):      1
Vendor ID:         AuthenticAMD
CPU family:        23
Model:             49
Model name:        AMD EPYC 7742 64-Core Processor
Stepping:          0
CPU MHz:           2245.780
BogoMIPS:          4491.56
Hypervisor vendor: KVM
Virtualization type:   full
L1d cache:         64K
L1i cache:         64K
L2 cache:          512K
L3 cache:          16384K
NUMA node0 CPU(s): 0-7
Flags:             fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm rep_good nopl cpuid extd_apicid tsc_known_freq pni pclmulqdq ssse3 fma cx16 sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm cmp_legacy cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw topoext perfctr_core ssbd ibpb stibp vmmcall fsgsbase tsc_adjust bmi1 avx2 smep bmi2 rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xgetbv1 nt_good arat umip
```

我可以执行 YSQLSH 以下面的方式连接到我的数据库。`alter role`命令更改 yugabyte 超级用户密码。不要让您关心的密码保留默认值:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl exec --namespace yb-demo -it yb-tserver-0 -- /home/yugabyte/bin/ysqlsh**
Defaulting container name to yb-tserver.
Use 'kubectl describe pod/yb-tserver-0 -n yb-demo' to see all of the containers in this pod.
Password for user yugabyte:
ysqlsh (11.2-YB-2.7.1.1-b0)
Type "help" for help.
yugabyte=# **select version();**
version
------------------------------------------------------------------------------------------------------------
 PostgreSQL 11.2-YB-2.7.1.1-b0 on x86_64-pc-linux-gnu, compiled by gcc (Homebrew gcc 5.5.0_4) 5.5.0, 64-bit
(1 row)
yugabyte=# **alter role yugabyte with password 'yb4oci4oke';**
ALTER ROLE
yugabyte=# \q
dev@cloudshell:~ (uk-london-1)$
```

请注意已定义的公共端点。如前所述，除了演示集群，这可能不是一个好主意:

```
dev@cloudshell:~ (uk-london-1)$ **kubectl --namespace yb-demo get services**
NAME                     TYPE               CLUSTER-IP         EXTERNAL-IP           PORT(S)                                                                          AGEyb-master-ui             LoadBalancer   10.96.156.48   152.67.136.107        7000:30910/TCP                                                                   12myb-masters               ClusterIP          None               <none>                7000/TCP,7100/TCP                                                                12myb-tserver-service   LoadBalancer   10.96.94.161   140.238.120.201   6379:31153/TCP,9042:31975/TCP,5433:31466/TCP                                     12myb-tservers              ClusterIP          N
```

这意味着我可以从任何 PostgreSQL 客户端连接到 YSQL 端口，比如:

```
psql postgres://yugabyte:yb4oci4oke@140.238.120.201:5433/yugabyte
```

从`kubectl --namespace yb-demo get services`你看到其他端口。5433 是 yb-tservers 通过负载均衡器公开的 YSQL (PostgreSQL 兼容)端点。在我的例子中，整个集群视图由 http://152.67.136.107:7000 的[上的 yb-masters 公开。](http://152.67.136.107:7000)

这里有一个例子:

![](img/fa406b81226a37af3a16ed24ba6a025b.png)

我已经截屏了，所以你看不到回收 t 恤的代码，但是，如果你安装了它，你就可以得到它。；)从 1/8 OCPU 运行 pgbench 客户端限制了读/写操作，但是 4 个 OCPU 节点可以做更多的事情。

# 库贝特尔

我发现 Cloud Shell 对于特别的命令非常方便，但是您可能希望在您的笔记本电脑上使用 oci-cli kubectl(下面的示例是在 Windows 上):

在 Powershell 上，以管理员身份安装 OCI CLI:

```
md oke
cd oke
Set-ExecutionPolicy RemoteSigned
Invoke-WebRequest [https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.ps1](https://raw.githubusercontent.com/oracle/oci-cli/master/scripts/install/install.ps1) -OutFile install.ps1
./install.ps1 -AcceptAllDefaults
cd ..
rd oke
```

在命令提示符下，设置到云租户的连接:

```
oci setup config
oci setup repair-file-permissions --file C:\Users\franc\.oci\oci_api_key.pem
type oci_api_key_public.pem
```

设置配置要求您从 web 控制台(右上角图标)获取一些 OCID:

*   租佃(【https://cloud.oracle.com/tenancy】T4)
*   用户设置([https://cloud.oracle.com/identity/users](https://cloud.oracle.com/identity/users))

生成的公钥必须在用户设置“添加 API 密钥”中添加给用户。我通常只留下 2 个 API 键，因为最大值是 3 个，当我需要做一些事情时，我不想被阻止。

# 清除

这是一个实验室。为了降低不使用时的成本，可以缩减到零个节点。当您想要删除集群时，还需要删除持久性卷:

```
dev@cloudshell:~ (uk-london-1)$ **helm uninstall yb-demo -n yb-demo**
release "yb-demo" uninstalled
dev@cloudshell:~ (uk-london-1)$ **kubectl delete pvc --namespace yb-demo --all**
persistentvolumeclaim "datadir0-yb-master-0" deleted
persistentvolumeclaim "datadir0-yb-master-1" deleted
persistentvolumeclaim "datadir0-yb-master-2" deleted
persistentvolumeclaim "datadir0-yb-tserver-0" deleted
persistentvolumeclaim "datadir0-yb-tserver-1" deleted
persistentvolumeclaim "datadir0-yb-tserver-2" deleted
persistentvolumeclaim "datadir1-yb-master-0" deleted
persistentvolumeclaim "datadir1-yb-master-1" deleted
persistentvolumeclaim "datadir1-yb-master-2" deleted
persistentvolumeclaim "datadir1-yb-tserver-0" deleted
persistentvolumeclaim "datadir1-yb-tserver-1" deleted
persistentvolumeclaim "datadir1-yb-tserver-2" deleted
dev@cloudshell:~ (uk-london-1)$ **kubectl delete namespace yb-demo**
namespace "yb-demo" deleted
```

现在，您可以终止集群。请注意，如果您没有执行之前的清理，数据块卷和负载平衡器将会保留。这是我在尝试这一切时的成本浏览器，使用 terraform 创建和删除集群，但没有清理安装:

![](img/cd2f5a10ca5fc5b5cec11191272d4a59.png)

请注意，除了价格之外，离开负载平衡器可能会阻止进一步的创建，因为它受每个租户的限制。

这种设置允许创建一个复制因子为 3 的 YugabyteDB 宇宙，其中滚动升级是可能的，因为节点可以重新启动，一次一个。使用 Kubernetes，您可以根据需要向外扩展，YugabyteDB 会自动管理碎片的重新平衡。当然，对于高可用性，您将在不同的可用性域中拥有节点(相当于 OCI 的 AZ)。这里的好处是，Oracle 云中可用性域之间的网络流量是免费的。你甚至可以在你的场所有一个节点，如果你有 FastConnect(相当于 OCI 的直接连接)，出口是免费的。

如果你 10 月份在欧洲，我会在克罗地亚甲骨文用户组:[https://2021.hroug.hr/eng/Speakers/FranckPachot](https://2021.hroug.hr/eng/Speakers/FranckPachot)的 HrOUG 演讲并演示这个。