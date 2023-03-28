# 针对在多个 EKS 集群上运行的服务，使用路由 53 分割流量

> 原文：<https://medium.com/codex/split-traffic-with-route-53-against-a-service-running-on-multiple-eks-clusters-34ce7d52f876?source=collection_archive---------2----------------------->

![](img/58edcfa18d106b760c149a85bfbf2ad5.png)

照片由[法鲁尔·阿兹米](https://unsplash.com/@fahrulazmi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/traffic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

网络流量控制是云环境的常见话题，尤其是在多集群环境中。我们的后端 Kubernetes 集群(EKS)托管数百个微服务，这些微服务服务于前端请求并与许多资源通信，包括 DynamoDB、RDS、MSK、Lambda 等，以及其他微服务。