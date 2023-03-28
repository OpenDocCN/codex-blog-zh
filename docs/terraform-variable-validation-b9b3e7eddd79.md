# Terraform 变量验证

> 原文：<https://medium.com/codex/terraform-variable-validation-b9b3e7eddd79?source=collection_archive---------0----------------------->

在这篇文章中，我将深入探讨 Terraform 中的变量验证，为什么你应该这样做，并展示许多有用的例子！

![](img/8c3c4cb11dc13a43bfad77663a39db82.png)

## 为什么要验证你的变量？

尽管您的 Terraform 的语法和配置可能有效，但传递到您的配置中的变量可能无效。传递无效变量通常会在部署阶段导致错误。