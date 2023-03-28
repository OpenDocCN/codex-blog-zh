# 3 个基本的 JavaScript 库

> 原文：<https://medium.com/codex/3-essential-javascript-libraries-88f6db34aa15?source=collection_archive---------17----------------------->

## *这些随时随地都可以用。*

![](img/22db01d78781dd7d507dabac5c4eb7ff.png)

照片由[丹尼尔·伊德里](https://unsplash.com/@ricaros?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/programming?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在 NPM 可以找到成千上万的图书馆。但是这些大多数完成简单的任务。我们有像'[是偶数](https://www.npmjs.com/package/is-even)'和'[是数字](https://www.npmjs.com/package/is-number)'这样的包。我们也有大型的实用程序库，比如下划线。*但是在大多数项目中，你实际上想要使用什么库呢？我们来查几个。*

# 国际光子

日期和时间对象操作非常困难。尤其是当你处理时区的时候。[时刻](https://momentjs.com/)时刻*曾经*完美地做到了这一点，但它已经慢慢地被[弃用](https://momentjs.com/docs/#/-project-status/)并被[取代**卢克森**和](https://moment.github.io/luxon/#/)。

Luxon 与 Moment 和其他竞争对手的区别在于使用了`Intl`对象，这是 ECMAScript 中用于时间和日期对象的更现代的 API。

# 洛达什

[**Lodash**](https://lodash.com/) 是一个非常有用并且*非常*知名的实用程序库。它提供了大量不同的函数，您可以从下划线对象(*中调用这些函数，就像 jQuery* 一样！).许多函数都是非常适合的，而有些函数对于诸如展平数组这样的任务非常有用。

我喜欢 Lodash 的一点是它的文档。文档对每一个函数都有很好的解释，并且提供了一个例子，这个例子提供了比简单的文本和函数名更好的见解。当您处理大量数组或嵌套数据时，这个库中几乎总会有一些有用的东西。

# Nodemon

[**Nodemon**](https://www.npmjs.com/package/nodemon) 是一个非常有用的开发实用工具。*它不需要编写任何代码*，只需要通过你的`npm run dev`命令调用。它通过在保存文件时自动重启开发服务器来简化开发。

它尝试只在更新服务器文件时重新启动服务器，并且在更新 HTML 或 CSS 文件时应该保持打开状态，对于这些文件，刷新浏览器就足够了。

# 结论

几乎在每个项目中都可以使用更多的库。当您同时运行多个进程时，[并发](https://www.npmjs.com/package/concurrently)可能会很有用，而 [Express](https://expressjs.com/) 对于您的服务器来说几乎每次都很棒。

非常感谢您的阅读，祝您度过美好的一天。如果有你经常使用的图书馆，请告诉我。