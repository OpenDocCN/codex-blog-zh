# 每个 Web 开发人员的 6 个基本 NPM 包

> 原文：<https://medium.com/codex/6-essential-npm-packages-for-every-web-developer-fe9ce278e573?source=collection_archive---------4----------------------->

## *几个非常有用的 NPM 包给大家。(附代码片段！)*

![](img/4ca003653b03584794f6d7a25d703ec5.png)

照片由[内森·达席尔瓦](https://unsplash.com/@silvawebdesigns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/javascript?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

许多 web 开发人员甚至无法想象一个没有 NPM 或 Yarn 的世界。NodeJS web 应用程序的大型包管理器。他们提供这么多很棒的图书馆。

但是它们也提供了大量没有被维护的库，甚至可能被废弃。这个列表是一个很小的库列表，它很可能会在未来相当长的一段时间内被维护，这样你就可以担心你的代码的质量，而不是你所使用的包。享受吧。

# 洛达什

我会一直提到这个美丽的图书馆，只要它是相关的。Lodash 是一个非常著名的实用程序库，它提供了几乎所有你能想到的函数。从字符串函数到处理对象的函数，Lodash 应有尽有。

**Lodash 最好的特性之一是它的文档化**。每一个功能都用文本段落和例子很好地解释了，以补充这些段落。如果你不知道一个函数是做什么的，快速看一下文档会给你很大的帮助。

# Nodemon

大多数开发人员的工具包中不可缺少的另一个包。Nodemon 是一个不需要任何代码的库，一旦你下载了它，它就依赖于 NPM 命令的使用。

```
nodemon index.js
```

一旦运行该命令，它将检查服务器文件中的任何更改。如果您保存了一个文件，Nodemon 将注册这个更改，并且它将为您快速重启服务器。这样可以保证你不用再`ctrl+c` `up` `enter`。这是非常有用的，一旦你习惯了它，它是令人惊讶的。

它也很容易安装。这就是为什么许多开发教程都以这个包为特色。

# 骗子

如果你是全栈开发人员，你可能比后端开发人员更需要这个。 [Faker](https://github.com/marak/Faker.js/) 允许我们轻松地为我们的 web 应用程序创建真实的“虚拟”数据。**当股东们四处观望，而你希望看起来比实际情况更好时，或者如果你需要在原型中创建视觉模型时，这很有用。**

# UUID

后端开发人员可能知道什么是 UUID。它是创建惟一 id 的通用标准，可以作为单独的类型在大多数数据库和编程语言中实现。这个 [JavaScript 包](https://www.npmjs.com/package/uuid)将为您创建有效的 UUIDs 并放入数据库。

它易于使用，其语法的简单性使其功能强大。再也不用担心你的身份证了。

# 国际光子

大多数前端开发人员以前都使用过 Moment。好吧，这就是时刻 2.0。这个库， [Luxon](https://moment.github.io/luxon/) ，是一个日期时间操作库，让日期操作变得更加容易。与 Moment 不同的是，它在引擎盖下使用了一个更现代的物体。**这让它比当下更能适应未来。**

对于 Moment 用户来说，语法非常相似，文档甚至有整整一章让 Moment 用户轻松进入 Luxon。试试看，不会失望的。

# Axios

对于后端开发人员来说， [Axios](https://github.com/axios/axios) 是一个 HTTP 请求库，比普通的 JavaScript 和 AJAX 更容易使用。T **您只需要输入最基本的东西**，Axios 会使用承诺来处理请求。

# 结论

这些只是所有伟大图书馆中的一小部分。看看我的其他博客帖子，或者去 NPM 看看，看看你是否能发现一些很棒的图书馆。

非常感谢您的阅读，祝您度过美好的一天。