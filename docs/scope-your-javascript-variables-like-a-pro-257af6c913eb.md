# 像专家一样确定 JavaScript 变量的范围

> 原文：<https://medium.com/codex/scope-your-javascript-variables-like-a-pro-257af6c913eb?source=collection_archive---------5----------------------->

![](img/2fad515588fd99bd85026411742c3582.png)

[凯·达姆斯](https://unsplash.com/@dilucidus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/bubble?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

当您开始您的 JavaScript 之旅时，您无疑会遇到被称为*范围* **的概念。我知道我遇到过，而且不止一次遇到过与范围概念相关的混淆错误或意外结果。**

范围的概念与变量交织在一起——每个容器都包含一个可变的值。在 JavaScript 中，变量可以用两种方式初始化:用`var`或…