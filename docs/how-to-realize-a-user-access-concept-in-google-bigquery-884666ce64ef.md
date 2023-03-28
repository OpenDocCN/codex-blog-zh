# 如何在 Google BigQuery 中实现一个用户访问概念

> 原文：<https://medium.com/codex/how-to-realize-a-user-access-concept-in-google-bigquery-884666ce64ef?source=collection_archive---------3----------------------->

## 您数据仓库的安全性

## 在 BigQuery 中使用会话用户

![](img/e044c5243f9f55f023db541cb9d57f57.png)

亚当·弗拉登伯格在 [Unsplash](https://unsplash.com/s/photos/lake?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

使用会话用户函数，您可以相对容易地通过 SQL 在 BigQuery 中开发用户访问概念。这里有一个简短的教程。

## 会话用户