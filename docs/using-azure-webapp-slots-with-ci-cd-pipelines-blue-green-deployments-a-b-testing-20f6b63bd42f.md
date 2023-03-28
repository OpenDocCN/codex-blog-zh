# 使用带有 CI/CD 管道的 Azure web app 插槽—蓝绿部署和 A/B 测试。

> 原文：<https://medium.com/codex/using-azure-webapp-slots-with-ci-cd-pipelines-blue-green-deployments-a-b-testing-20f6b63bd42f?source=collection_archive---------1----------------------->

Azure web 应用有插槽的概念。这些实际上是具有唯一 DNS 名称的 web 应用程序的副本，您可以将更新的应用程序部署到其中进行测试，然后交换插槽以使副本成为主应用程序。这些插槽通常被称为“暂存”和“生产”。

这被称为“蓝绿色”部署。交换以大爆炸的方式完成，最大限度地减少了任何停机时间。