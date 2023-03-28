# 如何用 AWS EC2 设置堡垒服务器

> 原文：<https://medium.com/codex/how-to-setup-bastion-server-with-aws-ec2-b1590d2ff815?source=collection_archive---------0----------------------->

## [法典](http://medium.com/codex)

您可能在 AWS 帐户中有各种各样的资源，如 EC2、S3、RDS、DocumentDB 等，并且您总是不想公开对它们的公共访问，那么您或开发人员如何安全地访问它们呢？

Bastion server 是一个常见的解决方案，它就像 AWS 中您的私有资源的接收者或前台。它确保访问者被授权或认证进入办公室或大楼内的人。

> 一个**堡垒主机**是一个服务器，其目的是提供…