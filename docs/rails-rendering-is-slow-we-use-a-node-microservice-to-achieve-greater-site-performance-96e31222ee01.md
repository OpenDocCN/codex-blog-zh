# Rails 渲染很慢——我们使用节点微服务来实现更好的站点性能

> 原文：<https://medium.com/codex/rails-rendering-is-slow-we-use-a-node-microservice-to-achieve-greater-site-performance-96e31222ee01?source=collection_archive---------12----------------------->

![](img/5d4f32f700b732e4df3dcd11041ac421.png)

Avel Chuklanov 拍摄的照片

# 对速度的需求

人们往往不耐烦。如果在任何流程中引入延迟，无论是在路上行驶还是被搁置，一个人的满意度都会下降。互联网也是如此。

根据谷歌[的研究，如果页面加载时间超过 2 秒](https://www.marketingdive.com/news/google-53-of-mobile-users-abandon-sites-that-take-over-3-seconds-to-load/426070/)，53%的网站访问被放弃，加载时间延迟 2 秒导致放弃率高达 87%。

除了客户满意度，搜索引擎排名还受到网站性能的严重影响。获得比你的竞争对手更高的排名相当于更高的知名度和更多的收入。这就是为什么我们不断努力改善 Spokeo 的技术体系，让我们的页面尽可能快地加载。

# 一切开始的地方

Spokeo 自 2006 年以来一直使用 Ruby on Rails (RoR)为其拥有数十亿唯一 URL 的大规模动态网站提供服务。

Rails 是一个模型-视图-控制器(MVC)框架，为数据库、web 服务和网页提供默认结构。通过使用 Rails，你可以很快建立一个新网站。它对开发人员非常友好，允许从想法开始到实现的快速周转时间。作为最流行的 MVC 框架之一，Rails 应该为它在将 Spokeo 带到我们今天的位置上所扮演的角色而受到称赞。

然而，RoR 也有一些缺点:

*   web 服务器的启动速度慢得令人难以置信。
*   内置渲染引擎性能很差。
*   Ruby 不擅长多线程。

与开发人员在创建应用程序时节省的宝贵时间相比，这些瓶颈并不重要——然而，随着网站变得成熟、功能齐全、流量巨大，它们开始成为一个重要的制约因素。

# 问题的症结

HAML。大声说出来，听起来很美味。这种 HTML 抽象语言是 RoR 标准，Spokeo 过去一直用它来构建网页模板。这在很长一段时间里都是一种解决方案。然而，随着 HAML 模板变得越来越复杂，我们的页面变得越来越慢。我们注意到在我们的 HAML 模板中，随着片段(包含在其他模板文件中的模板文件)数量的增加，渲染时间呈线性增加。例如，一个模板将具有以下内容:布局→页眉→内容(表格、循环→行部分)→页脚

所有这些很容易转化成 20-30 个部分。随着更多页面元素的加入，页面会进一步变慢。

![](img/c05b13a54594c2b1152073cb6263d807.png)

照片由拉多万[纳基夫·雷汉](https://unsplash.com/photos/cYyqhdbJ9TI?utm_source=unsplash&utm_medium=referral&utm_content=creditShareLink)拍摄

# 表演

Ruby 不是线程安全的。Rails 框架通过将所有东西包装在一个巨大的互斥锁中来保护我们免受这一事实的影响。为了满足 web 流量的高需求，我们求助于多个进程。过一段时间后，扩展就变得低效了。你花费了大量的时间来产生新的进程，这些进程耗尽了宝贵的系统资源。

# 解决办法

我们想提高网页渲染时间，但我们不想把婴儿和洗澡水一起倒掉。Spokeo 利用了 Rails 框架的大部分内容，我们对应用程序的所有部分进行了详尽的基准测试。经过多年的改进，我们认为最后也是最好的机会在于用更快的东西替换 Rails MVC 的“视图”部分。还是用 Node.js 吧！

使用 Node.js 有很多好处:

*   非阻塞输入输出
*   出色的异步请求处理
*   低内存占用
*   更智能的缓存
*   卓越的连接处理能力(以上因素的结果)

通过使用 Node.js 并利用 React 来呈现我们的页面，我们可以拥有我们的蛋糕并吃掉它。因此，我们建立了一个内部微服务器来渲染，代号为“萨尔萨”。

Salsa 是一个利用 React 来呈现页面的节点应用程序。我们在测试后发现，渲染时间并不像 HAML 那样随着页面上组件数量的增加而线性增加。

现在，我们可以保留 Rails 的灵活模型和控制器方面，同时使用 Node.js 的卓越速度查看视图，我们可以提高我们的站点性能并扩展更多的请求！

了解更多[人物搜索](https://www.spokeo.com/about)！

在 [Instagram](https://www.instagram.com/spokeo/) 、 [Twitter](https://twitter.com/Spokeo) 和 [LinkedIn](https://www.linkedin.com/company/167715/admin/) 上关注我们！

[我们正在招聘！](https://www.spokeo.com/careers/jobs)

作者

![](img/96c7ed785a1721dc4d5a7cf2d0ab11f8.png)

JP 阿塔拉，[高级开发运营工程师](http://linkedin.com/in/john-pierre-atallah-422b2189)