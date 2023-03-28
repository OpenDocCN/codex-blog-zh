# Webpack 简介

> 原文：<https://medium.com/codex/an-introduction-to-webpack-1f33475fd07b?source=collection_archive---------11----------------------->

## 如何使用模块捆绑器为生产优化我们的 JavaScript 代码

![](img/b840bbb6ec203ded55bb69b6ed014d65.png)

奥斯卡·伊尔迪兹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

你好！这是涵盖 Webpack 基础知识的系列文章的第一部分。我将介绍 Webpack 编译器的基本用法，以及如何通过创建*包*来利用代码优化。这一步在 web 开发过程中是至关重要的，因为它是我们定义依赖图、文件入口点、生成的块文件的目的地、非 JavaScript 模块化插件和非 JSON 文件(如 CSS 甚至图像文件)等的地方。好了，磨蹭够了，我们直接开始吧！

# Webpack 配置

Webpack 是一个 JavaScript 编译器，它将 JS 转换成模块化的*包*，为生产做准备。我们可以使用 Webpack 为我们的前端代码定义*入口点*，以及将其他文件转换成可读的 JavaScript 并提供包的路径作为输出。以下示例是` *config`* 对象的基本实现，该对象在 web 应用程序的` *src`* 文件夹中的` *index.js`* 处定义了一个入口点。

定义应用程序入口点的配置模块。(创建于 https://carbon.now.sh)

我们的 webpack 配置文件中的 entry 属性不需要是一个对象。左侧示例中的语法主要在定义多个入口点时使用。如果入口点是惟一的，我们也可以将入口点的路径定义为入口属性的值。

说到我们的配置模块的`*输出`,*尽管一个入口有能力保存多个入口点，但是只能定义一个输出路径。output 属性用于定义新创建的 JavaScript 包文件的目标路径以及文件名。换句话说，它定义了如何以及在哪里存储我们编译的代码。我们可以使用 Node.js ` *path`* 模块来实现这一点。

以下示例导入路径模块，并将创建的` *bundle.js`* 文件存储到` *dist`* 文件夹中:

将编译器的输出定义为一个对象，该对象使用 *path.resolve()* 将接收到的字符串解析为一个绝对路径，以及一个使用 name 值存储文件的 filename 属性。(创建于 [https://carbon.now.sh)](https://carbon.now.sh))

这里我们导入了 path 模块，它将帮助我们为编译后的代码定义一个文件路径。使用由导入的模块公开的` resolve()` 方法，我们使用` __dirname `全局对象将包文件分配到当前目录，并通过将文件的绝对路径字符串传递给` resolve() *`* 来为文件命名。

在构建我们的 Webpack 编译器时，我们还必须定义所需的任何加载器和插件。loader 属性用于转换任何非 JavaScript 文件(TypeScript、JSON、CSS 等)。)转换成可读的、编译过的 JavaScript。每种文件类型使用不同的加载器(即“css-loader”用于 css 文件，样式加载器可以处理 SCSS，文件加载器用于处理 JSON 数据等)。).它们被转换成添加到由 Webpack 生成的依赖图中的模块。下面的示例显示了 loader 属性的简单实现:

基于文件类型向模块添加构建规则。(创建于 https://carbon.now.sh)

上例中的*模块*对象定义了用于将代码转换成 JavaScript 模块的加载器。这些是从右到左执行的，或者更确切地说，是从下到上执行的，最后一个加载程序将输出 JavaScript 代码。如您所见，不同的文件类型需要不同的加载器。如果需要，您甚至可以使用` *module.rules`* 将多个加载器链接到一个文件类型:

链接 Webpack 加载程序并使用 options 属性进行配置(创建于 https://carbon.now.sh)

除了输入、输出和加载器，webpack 还使用*插件*来实现加载器无法实现的广泛功能。它可以将我们代码的编译记录到控制台，它可以与您的包一起构建一个 HTML 文件，或者存储环境变量以便用` *process.env `调用，*在您的包文件的顶部写注释掉的横幅，等等。下面的例子演示了一个配置文件，其中我们通过 NPM 导入了“HTMLWebpack”插件:

plugins 属性，使用内置插件和导入插件(在 [https://carbon.now.sh 创建)](https://carbon.now.sh))

这就是第 1 部分！请继续关注第 2 部分，我将深入探讨 webpack 插件，并探索可以使用 webpack 执行的各种任务。下次见！

赞美诗 Webpack 文档提供了所有可用插件的列表[此处](https://webpack.js.org/plugins/)。

## 引用的消息来源

> "网络包。"*网络包*，[http://webpack.js.org。于 2021 年 7 月 10 日访问。](http://webpack.js.org.)