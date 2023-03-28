# Cloudformation 模板制作正确

> 原文：<https://medium.com/codex/cloudformation-templating-done-right-c0acebd12d75?source=collection_archive---------1----------------------->

![](img/ad354076c6ab6da65e3e940c95cab0af.png)

克里斯里德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

您通常如何在不同的环境中设置您的 AWS？假设您有一个帐户，并且您的所有开发/试运行/生产都需要在同一个帐户中。

你通常做的是用 3 个 VPC 来隔离环境差异。假设你想用云的形成来做这件事。那么，你如何模板化它，以确保你有最佳的使用模板？