# 使用 Kustomize 和 Confd 笔记

> 原文：<https://medium.com/codex/using-kustomize-and-confd-notes-3b0b440eb4b5?source=collection_archive---------4----------------------->

写这篇文章是为了收集不同来源的笔记。

![](img/4af31fb4e0b85281bd96bb3535e182d8.png)

Kustomize[1]是一个独立的工具，可以通过 kustomization 文件定制 Kubernetes 对象。从 1.14 开始，Kubectl 还支持使用库化文件管理 Kubernetes 对象。要查看在包含 kustomization 文件的目录中找到的资源，请运行以下命令"[0]:

```
kubectl kustomize <kustomization_directory>
```

要应用这些资源，请使用 kustomize 或-k 标志运行 kubectl apply:

```
kubectl apply -k <kustomization_directory>
```

Kustomize 用于声明性对象配置。

什么是命令式和声明式对象配置？

# 命令性对象配置

在命令式对象配置中，kubectl 命令指定操作(创建、替换等。)、可选标志和至少一个文件名。指定的文件必须包含 YAML 或 JSON 格式的对象的完整定义。[2]

# 例子

创建在配置文件中定义的对象:

```
kubectl create -f nginx.yaml
```

删除两个配置文件中定义的对象:

```
kubectl delete -f nginx.yaml -f redis.yaml
```

# 权衡取舍

**与命令式命令相比的优势**:

对象配置可以存储在 Git 这样的源代码控制系统中。

对象配置可以与流程集成，例如在推送和审计跟踪之前检查更改。

对象配置提供了创建新对象的模板。

**与命令式命令相比的缺点**:

对象配置需要对对象模式有基本的了解。

对象配置需要编写 YAML 文件的额外步骤。

**与声明性对象配置相比的优势**:

命令式对象配置行为更简单，也更容易理解。

从 Kubernetes 版本开始，命令式对象配置更加成熟。

**与声明性对象配置相比的缺点**:

命令式对象配置最适用于文件，而不是目录。

对活动对象的更新必须反映在配置文件中，否则它们将在下次替换时丢失。

# 声明性对象配置

当使用声明性对象配置时，用户对本地存储的对象配置文件进行操作，然而，用户不定义要对文件采取的操作。kubectl 自动检测每个对象的创建、更新和删除操作。这允许在目录上工作，其中不同的对象可能需要不同的操作。

# 权衡取舍

与命令式对象配置相比的优势:

直接对活动对象所做的更改会被保留，即使它们没有合并回配置文件中。

声明性对象配置更好地支持对目录的操作和自动检测每个对象的操作类型(创建、修补、删除)。

与命令式对象配置相比的缺点:

当结果出乎意料时，声明性对象配置更难调试和理解。

使用 diffs 的部分更新会产生复杂的合并和修补操作。

# Kustomize 用法

*   从其他来源获取资源
*   为资源设置交叉域
*   编写和自定义资源集合

# 生成资源

"配置映射和机密保存由其他 Kubernetes 对象(如 pod)使用的配置或敏感数据。配置图或秘密的真实来源通常在集群外部，例如. properties 文件或 SSH 密钥文件。Kustomize 有 secretGenerator 和 configMapGenerator，它们从文件或文字生成 Secret 和 ConfigMap。[0]"

# configMapGenerator

要从文件生成配置映射，请在 configMapGenerator[0]的文件列表中添加一个条目

```
$ mkdir kustomizeDemo
$ cd kustomizeDemo
$ echo "ENV=DEMO" > demo.yaml
$ cat <<EOF >./kustomization.yaml
configMapGenerator:
- name: demo-configmap
  files:
- demo.yaml
  EOF
  $ kubectl kustomize ./
  apiVersion: v1
  data:
  demo.yaml: |
  ENV=DEMO
  kind: ConfigMap
  metadata:
  name: demo-configmap-47f78c8429
  Other approaches:
  configMapGenerator:
- name: example-configmap-1
  envs:
    - .env
      EOF
      cat <<EOF >./kustomization.yaml
      configMapGenerator:
- name: example-configmap-2
  literals:
    - FOO=Bar
      EOF
```

这是一个使用生成的配置图的示例部署:

```
# Create a application.properties file
cat <<EOF >application.properties
FOO=Bar
EOFcat <<EOF >deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  labels:
    app: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app
        image: my-app
        volumeMounts:
        - name: config
          mountPath: /config
      volumes:
      - name: config
        configMap:
          name: example-configmap-1
EOFcat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
configMapGenerator:
- name: example-configmap-1
  files:
  - application.properties
EOF
```

Kustomize 生成配置映射，配置映射映射到卷。

可以在[0]找到 secretGenerator。

# 设置交叉字段

在一个项目中为所有 Kubernetes 资源设置交叉字段是很常见的。设置交叉字段的一些用例:

*   添加相同的名称前缀或后缀
*   添加同一组标签
*   添加相同的注释集
*   为所有资源设置相同的命名空间

这里有一个例子:

```
# Create a deployment.yaml
cat <<EOF >./deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
EOFcat <<EOF >./kustomization.yaml
namespace: my-namespace
namePrefix: dev-
nameSuffix: "-001"
commonLabels:
  app: bingo
commonAnnotations:
  oncallPager: 800-555-1212
resources:
- deployment.yaml
EOF
```

# 构成和定制资源

## 组成

Kustomize 支持不同资源的合成。kustomization.yaml 文件中的 resources 字段定义了要包含在配置中的资源列表。在资源列表中设置资源配置文件的路径。下面是一个由部署和服务组成的 NGINX 应用程序的示例:

```
# Create a deployment.yaml file
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF# Create a service.yaml file
cat <<EOF > service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    run: my-nginx
EOF# Create a kustomization.yaml composing them
cat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
- service.yaml
EOF
```

kubectl kustomize 的资源。/包含部署和服务对象。

## 定制的

修补程序可用于对资源应用不同的自定义。Kustomize 通过 patchesStrategicMerge 和 patchesJson6902 支持不同的修补机制。patchesStrategicMerge 是文件路径列表。每个文件都应该被解析为一个战略性的合并补丁。修补程序中的名称必须与已经加载的资源名称相匹配。推荐做一件事的小补丁。例如，创建一个用于增加部署副本数量的补丁和另一个用于设置内存限制的补丁。

```
# Create a deployment.yaml file
cat <<EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  selector:
    matchLabels:
      run: my-nginx
  replicas: 2
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
EOF# Create a patch increase_replicas.yaml
cat <<EOF > increase_replicas.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3
EOF# Create another patch set_memory.yaml
cat <<EOF > set_memory.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  template:
    spec:
      containers:
      - name: my-nginx
        resources:
          limits:
            memory: 512Mi
EOFcat <<EOF >./kustomization.yaml
resources:
- deployment.yaml
patchesStrategicMerge:
- increase_replicas.yaml
- set_memory.yaml
EOF
```

运行 kubectl kustomize。/要查看部署:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      run: my-nginx
  template:
    metadata:
      labels:
        run: my-nginx
    spec:
      containers:
      - image: nginx
        name: my-nginx
        ports:
        - containerPort: 80
        resources:
          limits:
            memory: 512Mi
```

# CONFD

**confd** 是一款轻量级配置管理工具，专注于:

使用存储在 etcd、consul、dynamodb、redis、vault、zookeeper、aws ssm 参数存储或 env vars 和处理模板资源中的数据来保持本地配置文件最新。重新加载应用程序以获取新的配置文件更改[3]。

**AWS SSM**

AWS Systems Manager 允许您集中来自多个 AWS 服务的操作数据，并跨您的 AWS 资源自动执行任务。您可以创建资源的逻辑组，例如应用程序、应用程序堆栈的不同层，或者生产和开发环境。使用 Systems Manager，您可以选择一个资源组并查看其最近的 API 活动、资源配置更改、相关通知、操作警报、软件清单和修补程序符合性状态。您还可以根据操作需要对每个资源组采取措施。Systems Manager 提供了一个查看和管理 AWS 资源的中心位置，因此您可以完全了解和控制您的操作。[4]

Confd 与配置管理有关。这就是为什么我对 SSM 参数商店感兴趣。

**SSM 参数存储**

AWS Systems Manager 提供了一个集中的存储来管理您的配置数据，无论是纯文本数据(如数据库字符串)还是机密数据(如密码)。这允许您将您的秘密和配置数据从代码中分离出来。可以标记参数并将其组织成层次结构，从而帮助您更轻松地管理参数。例如，您可以使用相同的参数名称“db-string”和不同的层次路径“dev/db-string”或“prod/db-string”来存储不同的值。Systems Manager 与 AWS 密钥管理服务(KMS)相集成，允许您自动加密存储的数据。您还可以使用 AWS 身份和访问管理(IAM)来控制用户和资源对参数的访问。参数可以通过其他 AWS 服务引用，比如亚马逊弹性容器服务、AWS Lambda、AWS CloudFormation。[4]

一个这样的服务是 SSM 参数存储，它是一个安全的和管理的键/值存储，非常适合存储参数、秘密和配置信息。然而，在 2018 年 4 月，AWS 还推出了另一项名为 AWS Secrets Manager 的服务，提供类似的功能。鉴于这两种服务做的事情差不多，选择哪一种还不清楚。记住这一点，让我们看看这两种服务的相似之处和不同之处，以便更好地理解哪种服务最适合您的架构需求。[5]

SSM 参数商店是免费的，这就是为什么我更喜欢它而不是 AWS 的秘密管理器。

```
aws ssm put-parameter --name "/myapp/database/url" --type "String" --value "db.example.com"
aws ssm put-parameter --name "/myapp/database/user" --type "SecureString" --value "rob"
```

上述命令将两个参数放入 SSM 参数存储中

confdir 是存储模板资源配置和源模板的地方。

```
sudo mkdir -p /etc/confd/{conf.d,templates}
```

模板资源定义在 confdir 下的 [TOML](https://github.com/toml-lang/toml) 配置文件中。

/etc/confd/conf . d/my config . toml

```
[template]
src = "myconfig.conf.tmpl"
dest = "/tmp/myconfig.conf"
keys = [
    "/myapp/database/url",
    "/myapp/database/user",
]
```

源模板是 [Golang 文本模板](https://pkg.go.dev/text/template#pkg-overview)。

/etc/confd/templates/my config . conf . tmpl

```
[myconfig]
database_url = {{getv "/myapp/database/url"}}
database_user = {{getv "/myapp/database/user"}}confd -onetime -backend ssm$ cat /tmp/myconfig.conf 
[myconfig]
database_url = db.example.com
database_user = rob
```

0-)[https://github.com/kelseyhightower/confd](https://github.com/kelseyhightower/confd)

1-)【https://aws.amazon.com/systems-manager/features/ 

2-)[https://www . 1 strategy . com/blog/2019/02/28/AWS-parameter-store-vs-AWS-secrets-manager/](https://www.1strategy.com/blog/2019/02/28/aws-parameter-store-vs-aws-secrets-manager/)

3-)[https://kubernetes . io/docs/tasks/manage-kubernetes-objects/kustomization/](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/)

4-)[https://github.com/kubernetes-sigs/kustomize](https://github.com/kubernetes-sigs/kustomize)

5-)[https://kubernetes . io/docs/concepts/overview/working-with-objects/object-management/](https://kubernetes.io/docs/concepts/overview/working-with-objects/object-management/)