# NextJs 和服务器端渲染

> 原文：<https://medium.com/codex/nextjs-and-server-side-rendering-ssr-35b84afdc60d?source=collection_archive---------3----------------------->

## 介绍 NextJs 如何简化 React 服务器端渲染

![](img/00f14c271369f98f59e213856b38c8b2.png)

奥拉夫·阿伦斯·罗特内在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# NextJs 之前

尽管 React 已经存在了 7 年多，但关于它的最大争论之一是它在服务器端使用的有效性。许多开发人员抱怨说，他们需要大量的样板代码来呈现在服务器端提供的页面上。这使得开发人员更多地在客户端使用 React，并在服务器端寻找替代品。当时存在的一种模式是制作页面的核心构建块(从服务器获得服务)，并在客户端提供 React 服务，以某种方式实现它，使它能够操作 DOM 并注入更多组件。

# 应对服务器端的发展

上述场景是阻止许多大公司投资升级他们的堆栈以某种方式使用 React 的最大障碍。他们总是抱怨说，仅仅编写在客户端工作的代码是不够的。这并不意味着没有办法解决这个问题，开发者(直到今天)使用 ReactDOM 函数在服务器和客户端上合成和服务 React([查看这个例子](https://www.digitalocean.com/community/tutorials/react-server-side-rendering))。然而，NextJs 的引入使得生产就绪静态&服务器端代码变得更加容易，它具有许多其他生产功能，如智能捆绑，开箱即用，配置最少。

由于这篇文章关注的是 NextJs 中的服务器端渲染，我不会详细介绍 NextJs 本身是如何工作的，因为你可以随时查看他们的[文档](https://nextjs.org/)。

# getServerSideProps()函数

NextJs 允许开发人员按页面构建他们的应用程序，每个页面都是一个独立的入口点(就像一个单独封装和捆绑的迷你应用程序)，他们可以选择在客户端或服务器端呈现页面，或者通过从页面文件中导出某些函数来作为静态页面。

getServerSideProps()允许 NextJs 检测到这是一个服务器端呈现的页面，它不会在客户端级别上提供它，而是会在服务器上执行它，合成它的内容，并将其与服务器上预取的任何数据一起提供给客户端。

下面是该函数最简单的格式

因为 NextJs 在将抽象实现转换为本地实现方面做了很多工作，所以它提供了很棒的 [config](https://nextjs.org/docs/api-reference/next.config.js/introduction) 系统，扩展了开发人员配置和服务生产就绪代码的能力。configs 在如此多的属性中扩展了实现，从自定义头到路由以及它们之间的任何东西。这增加了另一个层次的灵活性，使得使用框架更加健壮和直观。

# 结论

NextJs 的出现有很多原因，服务器端渲染只是其中之一，尽管这绝对是他们迄今为止提供的最令人惊叹的功能之一，值得一提的是，它仍然缺乏一些核心概念，如完整的客户端路由，这是他们不支持的，因为他们认为 url 的变化是一个新页面路由的指示，这会导致到服务器的完整行程。你可以在这里阅读更多关于这个问题的[。此外，NextJs 在渲染和路由方面将每个页面视为一个独立的应用程序，因此它缺少 SPA 概念。](https://github.com/vercel/next.js/issues/8193)