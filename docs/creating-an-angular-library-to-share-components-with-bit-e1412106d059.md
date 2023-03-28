# 创建角度库以与 Bit 共享组件

> 原文：<https://medium.com/codex/creating-an-angular-library-to-share-components-with-bit-e1412106d059?source=collection_archive---------1----------------------->

## [法典](http://medium.com/codex)

Bit 是开发过程中非常有用的工具，可以很好地与 Angular 一起创建可重用的代码。它的设置也很简单，并且支持各种 javascript 框架。

下面是建立 Angular 库以在本地开发组件的快速演练，以及如何将它们上传到 B [it](http://bit.dev) 以便在您的或任何其他人的应用程序中重用。我强烈建议查看 Bit 文档，以便更好地理解正在发生的事情和最佳实践。

这些步骤是:

*   创建角度工作空间
*   创建一个库来存放要共享的组件
*   在您的工作空间中设置 Bit
*   编译并上传至 Bit

![](img/f94901dbfc57b21b4fb7b9649d56053f.png)

在云上，在奥豪湖/一旦你使用比特你会有什么感觉

## 角度库

首先，创建一个工作区，当出现提示时，对严格模式说是:

`ng new bit-components --create-application=false`

这将创建一个可以存放所有库的目录。

要实现的结构是每个库都成为 Bit 上可用的组件。如果你认为有一个更合适的方法来解决这个问题，请发表评论，让我们一起讨论。

因此，要创建保存组件的库:

`ng g lib nameOfComponent`

这已经创建了一个目录，其中包含您可能想要开始构建组件的文件。在这个目录中，将`lib`目录名改为你的组件名。即`projects/name-of-component/src/lib`变成了`projects/name-of-component/src/name-of-your-component`。Bit 使用这个最后的目录来命名站点/包下载上的组件。我建议浏览一下文档，看看是否有更合适的方法来做这件事。

这取决于你，编码你的组件！

## 与 Bit 共享

设置非常简单，上传也是如此。Bit 团队让这个过程变得非常顺利。

前往 B [it](http://bit.dev) ，创建账户，并创建收藏。

完成后，回到主目录(这里使用的示例是 bit-components ),运行以下命令来准备您的环境，构建组件并上传到 bit:

这是全球安装位，并链接到您的帐户。`bit init`命令准备工作空间以支持 Bit。然后添加 Angular 的编译器。`bit add`命令告诉 Bit 该工作空间中的哪些组件将被跟踪。

`--main`选项设置组件的入口点。任何您想提供给消费者的东西都应该在这个`public_api.ts`文件中导出。这可能包括其他类、接口、组件等，您希望在导入组件时随时可用。

`bit build`编译你的组件。`bit tag`为该工作区中所有被跟踪的组件设置一个版本号。`bit export`将此工作区中的所有组件推送到所选集合中。

打开你的浏览器，进入你的 [bit.dev](http://bit.dev) 页面。现在您应该可以看到您的组件了。右边是如何用 npm/yarn 等下载的说明。您的组件现在可以使用了！

这应该让您踏上了使用 Angular 和 Bit 轻松重用组件的道路。如果您有任何问题或认为有问题，请告诉我。