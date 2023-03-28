# Nodejs —依赖性管理

> 原文：<https://medium.com/codex/nodejs-dependency-management-d0e84fa595c0?source=collection_archive---------3----------------------->

# 介绍

Node.js 中的一个重要概念是，您想知道处理**依赖关系管理**的方式。这种依赖性管理是 core Node.js 体验的一部分。在这篇文章中，我们将学习各种依赖关系管理模式以及节点如何加载依赖关系。

因此，我们可以使用一个 **js 文件**来编写我们的应用程序，但是这不是模块化的。Node.js 使得编写模块化代码变得非常简单。

在我们深入细节之前，首先要回答的问题是**模块**。这是什么…