# 你用正确的方式创造 docker 形象了吗？

> 原文：<https://medium.com/codex/are-you-creating-docker-images-in-the-right-way-5f04806812af?source=collection_archive---------5----------------------->

![](img/c8d3771ba7664eecd01a87aa844525c3.png)

图片提供:Docker 主页

许多人可能认为 docker 文件很容易创建，它们有什么新功能？是的，它们的确很容易制作。但是，你能生成一个非常小并且能快速构建的 docker 图像吗？

下面给出了一个示例 dockerfile 来 dockerize 一个 Python Flask 应用程序。为了后面的目的，还可以使用命令`docker build -t` …构建 docker 映像