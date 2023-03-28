# 应用程序而不是库

> 原文：<https://medium.com/codex/applications-instead-of-libraries-383158addc04?source=collection_archive---------5----------------------->

在我们的[上一篇文章](https://www.aboutwayfair.com/careers/tech-blog/applications-instead-of-libraries-part-1)中，我们谈到了促使我们考虑为 Partner Home——way fair 的供应商门户——提供微前端架构的原因。现在是时候深入细节了。

在本帖中，我们将讨论如何利用模块联合来实现解耦应用，以改善供应商的体验。一个应用程序有前端和后端，它在我们的合作伙伴主门户中拥有完整的流程。我们将介绍如何设置使用远程应用程序的主机应用程序，以及我们如何使用这种模式构建微前端…