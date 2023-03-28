# 棱角分明的网络工作者

> 原文：<https://medium.com/codex/web-workers-in-angular-99fc4dac1d40?source=collection_archive---------0----------------------->

*了解如何在 Angular 中使用 web workers 进行后台处理*

![](img/19e728b63e309d8a0942c475e418cdf7.png)

在本文中，我将解释什么是 web workers，在哪里可以使用它们，以及如何在 Angular 中具体使用它们。如果你对如何用 Javascript 构建它们有一个大致的概念，那就太棒了。如果没有，我会保护你😎

开始吧！

# 什么是网络工作者？

Web workers 用于在后台线程中运行任何脚本，而不会干扰用户界面。因此，如果您的用户端有繁重的计算，您可以使用 web workers 来完成繁重的任务，同时允许浏览器为您的用户提供更流畅、更好的体验。

> 在 Angular 上下文中，这并不意味着 CLI 支持在 web worker 中运行 Angular。

# 创建 Web Worker

在 Angular 中创建一个 web worker 相当容易。您可以使用 Angular CLI 和使用`ng generate`命令。

`ng generate web-worker <location>`

该命令将执行以下操作:

1.  创建一个新文件`tsconfig.worker.json`。该文件如下所示。

2.创建`app.worker.ts`，这是您从工作人员那里获得响应的地方。

3.在 angular.json 中，在`build > options`中添加`”webWorkerTsConfig”: “tsconfig.worker.json”`，并为 web worker 注册生成的 tsconfig 文件。

4.你的`app.component.ts`也会被修改，这里会用到工人。

现在，如果您使用`npm start`运行 Angular 应用程序，您可以在控制台中看到上面的响应。

# 例子

让我们创建一个使用 web worker 的小应用程序。我已经创建了一个使用 web worker 计算阶乘的小应用程序。

在上面的代码中，您可以看到我们使用`worker.postMessage(factorialInput)`向 web worker 发送数据，并且我们在`worker.onmessage`属性中接收来自 worker 的响应。

因此，您可以在项目中使用 web workers 并优化您的应用程序。对于优化角度应用的其他方法，请阅读[减少角度应用的初始加载时间](/khojchakra/reduce-initial-load-time-of-angular-application-9b9ed4a6bfc9)。

快乐编码😋