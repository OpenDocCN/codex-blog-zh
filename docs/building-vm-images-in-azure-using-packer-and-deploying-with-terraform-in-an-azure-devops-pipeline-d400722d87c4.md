# 在 Azure DevOps 管道中，使用 Packer 在 Azure 中构建虚拟机映像，并使用 Terraform 进行部署！

> 原文：<https://medium.com/codex/building-vm-images-in-azure-using-packer-and-deploying-with-terraform-in-an-azure-devops-pipeline-d400722d87c4?source=collection_archive---------1----------------------->

![](img/c2cf3da67c13eac13c891d3233e72de2.png)

布莱克·康纳利在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 Azure 中部署一个 VM 当然很好，但是 VM 本身的基本配置如何呢？这就是[打包器](https://www.packer.io/)的用武之地，它可以创建一个定制映像，并配置服务器角色、应用程序、包等。根据需要。该自定义映像可用于构建您的虚拟机。正在创建它…