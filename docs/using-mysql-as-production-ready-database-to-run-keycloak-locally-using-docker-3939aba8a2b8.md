# 如何使用 Docker 将 MySQL 作为生产就绪数据库运行 Keycloak

> 原文：<https://medium.com/codex/using-mysql-as-production-ready-database-to-run-keycloak-locally-using-docker-3939aba8a2b8?source=collection_archive---------0----------------------->

在之前的一篇文章中([此处](https://migueldoctor.medium.com/run-keycloak-locally-using-docker-and-secure-your-apps-in-3-steps-f6c554427e54))，我们谈到了**身份和访问管理(IAM)解决方案**，它们是什么，以及这种解决方案如何帮助我们加快开发速度，同时提高应用的安全性。也就是说，我们专注于 [***Keycloak***](https://www.keycloak.org/) ，这是一个开源平台，旨在用很少甚至没有代码来支持现代应用和服务。此外，在前面提到的帖子中，我们解释了如何使用 docker 启动并运行一个全功能的 ***Keycloak*** 实例，然后我们执行了一些基本的安全实现，如…