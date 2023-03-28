# 用 Jest 对 AWS SDK v3 进行单元测试:测试 S3

> 原文：<https://medium.com/codex/unit-tests-for-aws-sdk-v3-with-jest-testing-s3-a271163d2d2d?source=collection_archive---------2----------------------->

![](img/a2b5cc0ed1684dd7c363460a493f983d.png)

Even cats hate broken unit tests. Photo by [傅甬 华](https://unsplash.com/@hhh13?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/frustrated?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText).

# 介绍

## 早在 2020 年 12 月，AWS SDK for Javascript 的第 3 版就已经上市。这带来了打字的改进，因为它已被改写成打字稿。引入新的中间件堆栈和模块化…