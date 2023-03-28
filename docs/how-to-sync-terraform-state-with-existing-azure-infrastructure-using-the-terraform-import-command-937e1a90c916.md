# 如何使用 Terraform Import 命令将 Terraform 状态与现有 Azure 基础架构同步

> 原文：<https://medium.com/codex/how-to-sync-terraform-state-with-existing-azure-infrastructure-using-the-terraform-import-command-937e1a90c916?source=collection_archive---------3----------------------->

## 包含两个真实例子的详细指南。

![](img/36473dd43cc9a335967d59d671219452.png)

肖恩·昂在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果你正在阅读这篇文章，你可能正处于一个不得不使用 Terraform 来管理你的云基础设施的情况。但是现有的资源，不是使用 Terraform 创建的，需要 it 来管理。