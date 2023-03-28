# Jenkins 安全设置，支持基于 Linux 用户的身份验证

> 原文：<https://medium.com/codex/jenkins-security-setup-enabling-linux-users-based-authentication-e017f1f8a1b4?source=collection_archive---------0----------------------->

![](img/3757c3bac5aeb15a22d21d5e95da56e8.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上 [Sai Kiran Anagani](https://unsplash.com/@_imkiran?utm_source=medium&utm_medium=referral) 拍摄的照片

最近在工作中，我们希望用户能够作为个人用户登录到 Jenkins UI，这样任何正在运行的作业都可以追溯到个人。虽然我们使用谷歌作为我们的电子邮件提供商，但问题是它将使公司的每个人都能登录到 Jenkins。我们有使用詹金斯自己的数据库的设施，但我想要更安全的东西。
詹金斯已经…