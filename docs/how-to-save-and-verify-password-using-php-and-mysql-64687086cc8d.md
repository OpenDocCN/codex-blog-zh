# 如何使用 PHP 和 MySQL 保存和验证密码

> 原文：<https://medium.com/codex/how-to-save-and-verify-password-using-php-and-mysql-64687086cc8d?source=collection_archive---------1----------------------->

## 使用 password_hash()和 password_verify()函数。

![](img/1e04059c867bd353f4a3ce23abae21e3.png)

本在 [Unsplash](https://unsplash.com/s/photos/php?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

在本教程中，你将学习如何保存密码，并使用 **PHP** 和 **MySQL** 验证保存的密码。在软件应用程序开始时，开发人员将密码以纯文本的形式保存在数据库表中。当数据库被黑客入侵时，他们可以查看…