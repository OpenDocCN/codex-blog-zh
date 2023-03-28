# 为 AWS 步骤函数创建 Jest 单元测试

> 原文：<https://medium.com/codex/creating-jest-unit-tests-for-aws-step-functions-4e4d619789b3?source=collection_archive---------6----------------------->

![](img/2bc743a8b2b0e8dee97b8b7d2215d517.png)

是的，这个试管里肯定有红色液体。来自 [Pexels](https://www.pexels.com/photo/close-up-photo-of-a-man-doing-an-experiment-8326308/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[金德媒体](https://www.pexels.com/@kindelmedia?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的照片

对单元测试的一个常见误解是测试的创建者会试图测试太多。认为软件是一个[黑盒](/@mcccccc/difference-among-white-box-black-box-and-grey-box-testing-35482292473f)似乎很正常，测试是理解黑盒做什么的一种方式。但有时你必须学会热爱黑盒和它所包含的秘密。你的单元测试不需要知道的秘密。

# 拥抱神谕…