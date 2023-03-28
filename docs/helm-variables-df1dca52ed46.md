# 舵-变量

> 原文：<https://medium.com/codex/helm-variables-df1dca52ed46?source=collection_archive---------2----------------------->

## 舵模板变量概述

![](img/60338065d46688a6f3b2446425c9d820.png)

Vishnu Mohanan 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在**舵**模板中，变量的使用频率较低。但是我们将看到如何使用它们来简化代码，并更好地利用`with`和`range`。

要深入了解`with`和`range`请阅读本文: [**舵—流量控制**](/@shamimice03/helm-flow-control-a085a67f22e)

## **变量初始化:**

```
**{{- $namespace := .Release.Namespace -}}**
```

## “with”中变量的用法:

在[Helm—Flow Control](/@shamimice03/helm-flow-control-a085a67f22e)**文章中，我们已经看到下面的代码会抛出一个错误:**

```
{{- with .Values.configMap.data.conf }}
  operating-system: {{ .os }}
  database-name: {{ .database }}
 **k8s-namespace: {{ .Release.Namespace }}** {{- end }}
```

**`Release.Namespace`不在`with`区块限定的范围内。解决这个问题的一个方法是在 Release 对象前面使用 **$** 符号。因为根作用域也由 **$** 符号表示。如果我们将代码改为`$.Release.Namespace`，那么问题将会得到解决。**

**或者，也可以使用**变量解决上述问题。****

```
**{{- $namespace := .Release.Namespace -}}**
{{- **with** .Values.configMap.data.conf }}
  operating-system: {{ .os }}
  database-name: {{ .database }}
 **namespace: {{ $namespace }}** {{- end }}
```

**在上面的演示中，在定义`with`块之前，我们指定了`$namespace := .Release.Namespace`。然后，在`with`块中，我们使用了`$namespace`变量，因为它仍然指向发布名称空间。**

## **“范围”内变量的使用:**

**变量在`range`循环中特别有用。它们可以在类似列表的对象上使用，以捕获**索引**和**值**:**

**假设我们有包含以下条目的 **values.yaml** 文件:**

```
**# values.yaml**configMap:
  data:
    platfrom:
     - java
     - python
     - golang
```

**我们想要生成一个**配置映射**清单文件，如下所示:**

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: release-name-configmap
**data: 
  0 : "java" 
  1 : "python" 
  2 : "golang"**
```

**为了生成如上所示的清单文件，我们可以一起使用`range`和**变量**来编写一个 **configmap.yaml** 模板文件:**

```
**# configmap.yaml**apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:  
  {{- **range** $index, $value := .Values.configMap.data.platfrom }} 
 **{{ $index }} : {{ $value | quote }}** 
  {{- end }}
```

**对于既有一个**键**又有一个**值**的数据结构，我们可以使用`range`来获得两者。**

**假设，我们必须为 Kubernetes **Secrets** 对象编写一个模板文件。目前，我们在 **values.yaml** 文件中有以下条目:**

```
**# values.yaml**secrets:
  db:
    MYSQL_USER: admin
    MYSQL_PASSWORD: Admin@123
```

**在 Kubernetes **Secrets** 中，`data`字段中所有键的值必须是 **base64 编码的**字符串。要深入了解 Kubernetes 的秘密，请阅读本文: [**秘密**](https://faun.pub/secrets-kubernetes-298ea8dd9911)**

**我们希望生成一个 Kubernetes 秘密清单文件，如下所示:**

```
apiVersion: v1
kind: Secret
metadata:
  creationTimestamp: "2022-10-12T04:11:18Z"
  name: mysql-secret
  namespace: default
  resourceVersion: "11349"
  uid: 85dec499-2744-4062-9deb-1e9f1dd71fb6
type: Opaque
**data:
  MYSQL_PASSWORD : QWRtaW5AMTIz
  MYSQL_USER : YWRtaW4=**
```

**要生成如上所示的清单文件，我们可以通过以下方式创建一个模板文件:**

```
**# secretes.yaml**apiVersion: v1
kind: Secret
metadata:
  creationTimestamp: "2022-10-12T04:11:18Z"
  name: mysql-secret
  namespace: default
  resourceVersion: "11349"
  uid: 85dec499-2744-4062-9deb-1e9f1dd71fb6
type: Opaque
data:
 **{{- range $key, $value := .Values.secrets.db }}
  {{ $key }} : {{ $value | b64enc }}
  {{- end }}**
```

**注意，我们使用了两个变量`$key`和`$value`来从驻留在 **values.yaml** 文件中的`.Values.secretes.db`对象获取键和值。除此之外，我们还使用了`b64enc`函数将明文转换为 **base64 编码的**格式。**

> **我希望这篇文章能帮助你理解如何在 helm 模板文件中使用变量。**
> 
> ***如果你觉得这篇文章很有帮助，请* ***别忘了*** *去点击* ***拍拍*******跟着*** *按钮帮我写更多这样的文章。
> 谢谢🖤****

## ***参考***

***[](https://helm.sh/docs/chart_template_guide/variables/) [## 变量

### 有了函数、管道、对象和控制结构，我们可以转向一个更基本的概念…

helm.sh](https://helm.sh/docs/chart_template_guide/variables/)***