# WSO2 的分步指南是带有负载平衡的 worker-manager 集群。

> 原文：<https://medium.com/codex/a-step-by-step-guide-to-wso2-is-worker-manager-clustering-with-load-balancing-63e11d9e7946?source=collection_archive---------5----------------------->

![](img/be2205c412e650f91b70c6b77f93ac6f.png)

WSO2 产品的多个节点可以以集群模式部署，以实现更大的可扩展性、更高的资源可用性、战略性的资源使用和简化的管理。在生产环境中使用这种方法有助于提高性能，方法是在逻辑上处理流量的同时，跨节点无缝地分发请求，从而实现高集群吞吐量。简而言之，如果集群中的一台服务器出现故障，集群中配置的其他服务器可以接管工作负载。负载平衡器用于在集群中的节点之间分发请求。在这篇博客中，我将使用 NGINX 负载平衡器来实现这个目的。

## 什么是工作管理器集群设置？

Worker-manager 集群是一个部署模型，由“Worker”节点和“management”节点组成。基本上，worker 节点用于服务客户端收到的请求，而 management 节点负责与管理相关的任务，比如部署和配置工件。

在下一节中，我将指导您使用 MYSQL 数据库建立一个具有负载平衡的双节点 worker-manager 集群环境。

# 1.设置 WSO2 的 2 个节点是

**1.1** 从[这里](https://wso2.com/identity-server/)下载最新的 WSO2 身份服务器。有关运行身份服务器的详细信息，请参见[运行产品](https://is.docs.wso2.com/en/latest/setup/running-the-product/)。我在这里将它称为“管理器”节点。

**1.2** 获取下载的 WSO2 的一个副本是 pack，从这里开始称为‘worker’节点。

**1.3** 将以下配置添加到路径<IS _ HOME>/repository/conf 中两个节点的 deployment.toml 文件中，以便在它们之间启用集群。我使用了众所周知的地址(WKA)成员方案进行聚类。

您需要指定用于传递集群消息的 IP 地址和端口。每个节点的端口号应该是唯一的。

```
[clustering]
membership_scheme = "wka"
local_member_host = "<IP_Address_of_the_editing_node>"
local_member_port = "<Port_number>"
members = ["<IP_Address_of_the_editing_node>:<Port_number>", "<IP_Address_of_the_other_node>:<Port_number>"]
```

例如，下面是我的配置。

```
**For manager node,** [clustering]
membership_scheme = "wka"
local_member_host = "127.0.0.1"
local_member_port = "4001"
members = ["127.0.0.1:4000", "127.0.0.1:4001"]**For worker node,**[clustering]
membership_scheme = "wka"
local_member_host = "127.0.0.1"
local_member_port = "4000"
members = ["127.0.0.1:4000", "127.0.0.1:4001"]
```

您可以从[这里](https://is.docs.wso2.com/en/latest/administer/clustering-overview/#about-membership-schemes)了解更多关于集群成员方案的信息，以决定最适合您需求的方案。

**1.4** 更改`<IS_HOME>/repository/conf/deployment.toml`文件，使用主机名而不是原始 IP 访问服务器。`hostName`应该解析为负载均衡器前端 IP 地址。

```
[server]
hostname = "wso2.is"
```

将这个主机名映射也添加到`etc/hosts`文件中。

**1.5** 我们需要为一个 IS 节点设置不同的端口偏移值，因为两个节点将在同一台机器上运行。因此，只需在<IS _ HOME>/conf/deployment . toml 文件中向 worker 节点添加以下配置。

```
[server]
offset = "1"
```

**1.6** 导航到`<IS_HOME>/repository/conf/deployment.toml`文件，将代理端口设置为`443`，如下图所示。这是负载平衡器前端端口。

```
[transport.http.properties]
proxyPort = 80
[transport.https.properties]
proxyPort = 443
```

# 2.配置数据库

**2.1** 在您的 2 个节点中导航到<IS _ HOME>/repository/conf，并将两个 deployment.toml 文件配置为指向数据库。

下面是一个配置示例。

```
[user_store]
type = "database"[database.identity_db]
type = "mysql"
hostname = "localhost"
name = "regdb?verifyServerCertificate=false&amp;useSSL=false&amp;requireSSL=false"
username = "root"
password = "root"
port = "3306"[database.shared_db]
type = "mysql"
hostname = "localhost"
name = "regdb?verifyServerCertificate=false&amp;useSSL=false&amp;requireSSL=false"
username = "root"
password = "root"
port = "3306"
```

有关在 WSO2IS 中使用数据库的更多信息，请参考本[文档](https://is.docs.wso2.com/en/latest/setup/working-with-databases/)。

**2.2** 接下来，将 MySQL JDBC 驱动添加到<PRODUCT _ HOME>/repository/component/lib 目录中。

最后，您需要执行数据库脚本，以便在本地创建数据库。这可以在服务器启动时使用`-Dsetup` 选项自动完成，也可以使用 MySQL workbench、DBeaver 等手动运行脚本。

# 3.配置 NGINX 负载平衡器

现在我们已经完成了集群的设置，让我们开始用 NGINX 负载均衡器来处理它。

首先你需要在你的本地机器上安装 NGINX。NGINX 有两个版本，一个是 plus，另一个是 community 版本，我在这次演示中使用了 community 版本。

**3.2** 导航到`/etc/nginx/conf.d`目录，创建一个 VHost 文件，命名为`is.http.conf`

添加以下配置并保存文件。通过这种配置，Nginx 将使用`http://is.wso2.com/`通过 HTTP 80 端口将 HTTP 请求定向到两个 is 节点。

```
upstream wso2.is.com {
    server 127.0.0.1:9443;
    server 127.0.0.1:9444;
}server {
listen 80;
    server_name is.wso2.com;
    location / {
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_read_timeout 5m;
            proxy_send_timeout 5m;
            proxy_pass [http://wso2.is.com](http://wso2.is.com);proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
}
```

3.3 以同样的方式，让我们配置 NGINX 来引导 HTTPS 请求，通过 HTTPS 443 端口使用`https://is.wso2.com/`

创建另一个 VHost 文件，并在同一个`/etc/nginx/conf.d`目录中将其另存为`is.https.conf`。向其中添加以下配置。

```
upstream ssl.wso2.is.com {
    server 127.0.0.1:9443;
    server 127.0.0.1:9444;
}server {
listen 443;
    server_name is.wso2.com;
    ssl on;
    ssl_certificate /usr/local/etc/nginx/ssl/wso2.com.crt;
    ssl_certificate_key /usr/local/etc/nginx/ssl/wso2.com.key;
    location / {
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_read_timeout 5m;
            proxy_send_timeout 5m;
            proxy_pass [https://ssl.wso2.is.com](https://ssl.wso2.is.com);proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
}
```

提示:将`ip_hash`配置添加到文件中，并在尝试访问管理控制台时重新加载 nginx。

```
upstream ssl.wso2.is.com {
    server 127.0.0.1:9443;
    server 127.0.0.1:9444;
    ip_hash;
}
```

3.3 为 HTTP 和 HTTPS 请求添加上述两个配置后，打开一个终端并运行下面的命令来重新加载 NGINX 服务器。

```
$sudo service nginx reload
```

要进一步了解负载平衡功能和更多配置，请访问这里的。

现在我们已经完成了必要的配置，让我们启动管理器和工作节点服务器。

*   导航到管理器节点的`<IS_HOME>/bin` 文件夹，打开终端并运行以下命令。

```
sh wso2server.sh
```

一旦服务器启动，您将在终端中看到如下日志。

```
INFO {org.wso2.carbon.core.clustering.hazelcast.HazelcastClusteringAgent} - Elected this member [xxxxx-xxxxx-xxxxx-xxxxx] as the Coordinator node
```

*   类似地，导航到 Worker 节点的`<IS_HOME>/bin` 文件夹，并在终端中运行相同的命令。

```
sh wso2server.sh
```

启动 Worker 节点后，将为充当 Hazelcast 协调器的 Manager 节点打印类似于以下内容的日志，表明 Worker 节点已成功加入集群。

```
INFO {org.wso2.carbon.core.clustering.hazelcast.wka.WKABasedMembershipScheme} - Member joined [xxxxx-xxxxx-xxxxx-xxxxx]: /127.0.0.1:4000
```

*   此外，尝试关闭 Manager 节点，您将看到剩余的 worker 节点被选为集群的新协调器，如下所示。

```
INFO {org.wso2.carbon.core.clustering.hazelcast.HazelcastClusteringAgent} — Elected this member [xxxxx-xxxxx-xxxxx-xxxxx] as the Coordinator nodeINFO {org.wso2.carbon.core.clustering.hazelcast.wka.WKABasedMembershipScheme} — Member left [xxxxx-xxxxx-xxxxx-xxxxx]: /127.0.0.1:4001
```

如果您可以观察到上述行为，这意味着您已经成功地建立了一个 worker-manager 集群。恭喜:)

希望现在您对如何通过适应工人/经理集群模式而不完全依赖产品的单个实例来轻松解决可靠性和可用性问题有了更全面的了解。

欲了解更多信息，请访问 WSO2 身份服务器[文档](https://is.docs.wso2.com/en/latest/setup/deployment-guide/)。

感谢您的阅读！

## 参考

 [## WSO2 身份服务器文档

### WSO2 Identity Server 是一个 API 驱动的开源 IAM 产品，旨在帮助您构建有效的 CIAM 解决方案。它…

is.docs.wso2.com](https://is.docs.wso2.com/en/latest/)  [## 群集多个 MicroStrategy 服务器

### 一组集群化的机器向一组公共用户提供一组相关的功能或服务。微观战略…

doc-archives.microstrategy.com](https://doc-archives.microstrategy.com/producthelp/10.11/SystemAdmin/WebHelp/Lang_1033/Content/Clustering.htm)