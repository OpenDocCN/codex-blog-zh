# 如何在 React 中使用 Promise.all 来加速 API 请求

> 原文：<https://medium.com/codex/how-to-use-promise-all-in-react-to-speed-up-api-requests-f13250d64491?source=collection_archive---------17----------------------->

无论何时创建 React 应用程序，发出 API 请求实际上都是一种需要。他们可以非常慢(像 https://www.twilio.com 的)或者超级快(https://www.telize.com 的)。如果您的应用程序很复杂，要在缓存之前获取必要的数据，您可能需要使用两个或更多 API 调用，即使使用快速 API，它也可能仍然很慢。在本文中，我们将创建一个环境来尽可能快地获取数据。

![](img/12e5ac84e1a9132ebaeeb0891e354b44.png)

当我们第一次在[bitraphorn](https://bitraptors.com/)开始使用 Strapi 时，我不得不开始考虑 api 请求的新方法。

```
💡 Strapi = A Headless Content Manager System, basically a customizable content API
```

对页面上的内容(如标题、副标题、正文、图片等)有一个调用。)，可能还会有更多的博客帖子、用户评论和其他内容。在缓存之前，单个页面可能偶尔需要两到三次调用，这可能会非常慢。

我的高级开发同事随后向我演示了如何使用`Promise.all()`来加速这个过程。

在此之前，我的 API 调用如下所示:

```
❌ THIS PATTERN COULD BE VERY SLOW, THEY RUN AFTER EACH OTHERconst pageData = await axios.get(...)
const blogPostsData = await axios.get(...)
const reviewsData = await axios.get(...)
```

关键字`await`导致调用顺序运行，而不是并行运行，这是这个解决方案中的问题；整个过程需要 3-6 秒，1-2 秒的 API 调用，这是一个糟糕的 web 体验。

*最好的选择是并行运行这些调用，只需要三个调用中最慢的一个调用完成。*

这就是`Promise.all`的作用

```
🔥 WITH Promise.all() YOU CAN RUN THEM IN PARALLEL WITHOUT awaitconst [ pageData, blogPostsData, reviewsData ] = await Promise.all([
  axios.get(...),
  axios.get(...),
  axios.get(...)
])
```

本质上，您必须在一个数组中声明您的数据变量，并且根据变量的顺序将调用传递给`Promise.all()`的数组。

请注意，在这种情况下，您不需要将`await`放在呼叫前面；如果您这样做了，调用将一个接一个地执行，从而消除了好处！

👋我希望这在你寻找新的学习材料时对你有用。感谢您花时间阅读这篇文章！

如果你从我的文章中更好地理解了一些东西，你可以请我喝咖啡来支持我:[https://www.buymeacoffee.com/daanworks](https://www.buymeacoffee.com/daanworks)

📯订阅我的电子邮件列表，如果你想在未来读到更多这样的故事，请在这里或 Twitter 上关注我

# Bit:构建更好的 UI 组件库

向[的**位**的](https://bit.cloud)问好。它是组件驱动应用程序开发的首选工具。

使用 Bit，您可以将应用程序的任何部分创建为可组合和可重用的“组件”。您和您的团队可以共享一个组件工具箱，以便更快、更一致地构建更多应用。

*   创建和编写“**应用构建模块**”:UI 元素、完整功能、页面、应用、无服务器或微服务。任何 JS 栈。
*   轻松地**共享，并作为一个团队重用**组件。
*   快速**跨项目更新** **组件**。
*   化难为易: [Monorepos](https://bit.cloud/blog/painless-monorepo-dependency-management-with-bit-l4f9fzyw) ，[设计系统](https://blog.bitsrc.io/how-we-build-our-design-system-15713a1f1833) & [微前端](https://blog.bitsrc.io/how-we-build-micro-front-ends-d3eeeac0acfc)。

[**尝试 Bit 开源免费→**](https://bit.cloud)

[![](img/9e504def49038e8ee350049355d52d01.png)](https://bit.cloud)

# 了解更多信息

[](https://blog.bitsrc.io/how-we-build-micro-front-ends-d3eeeac0acfc) [## 我们如何构建微前端

### 构建微前端来加速和扩展我们的 web 开发过程。

blog.bitsrc.io](https://blog.bitsrc.io/how-we-build-micro-front-ends-d3eeeac0acfc) [](https://blog.bitsrc.io/how-we-build-our-design-system-15713a1f1833) [## 我们如何构建一个组件设计系统

### 用组件构建一个设计系统来标准化和扩展我们的 UI 开发过程。

blog.bitsrc.io](https://blog.bitsrc.io/how-we-build-our-design-system-15713a1f1833) [](https://bit.cloud/blog/how-to-reuse-react-components-across-your-projects-l3bhezsg) [## 如何在项目中重用 React 组件

### 最后，您完成了在应用程序中为时事通讯创建一个奇妙的输入字段的任务。你对…很满意

比特云](https://bit.cloud/blog/how-to-reuse-react-components-across-your-projects-l3bhezsg) [](https://blog.bitsrc.io/5-ways-to-build-a-react-monorepo-a294b6c5b0ac) [## 构建 React Monorepo 的 5 种方法

### 构建生产级 React monorepo:从快速构建到代码共享和依赖。

blog.bitsrc.io](https://blog.bitsrc.io/5-ways-to-build-a-react-monorepo-a294b6c5b0ac)