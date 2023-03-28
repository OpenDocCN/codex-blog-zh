# 带 Istio 代理边车的 Nginx 入口控制器的出口流量控制

> 原文：<https://medium.com/codex/egress-traffic-control-for-nginx-ingress-controller-with-istio-proxy-sidecar-ef8f19902b43?source=collection_archive---------3----------------------->

![](img/cdc03593c8f611574621882c55aae61a.png)

出口流量是来自 Kubernetes 集群中的 pod 的出站网络连接或请求。默认情况下，一个 Pod 可以向任何其他 Pod 发送请求，甚至可以访问互联网。不过，它可能有安全问题。有时，我们只是希望一个 Pod 只能访问其他 Pod 或允许的外部网站。