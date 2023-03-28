# 在 3 分钟内构建一个节点服务器

> 原文：<https://medium.com/codex/build-a-node-server-in-3-minutes-ea395e369e5b?source=collection_archive---------10----------------------->

## 使用 Node.js 创建服务器的最快教程

我相信 JavaScript 是最好的语言之一。您可以用同一种语言创建前端和后端应用程序，并且它本机处理 JSON 对象。今天，让我们来看看使用 Node.js 启动后端服务器有多简单。

![](img/72447cf1f6dfd252aabacf07d12d33a2.png)

照片由[乔丹·哈里森](https://unsplash.com/@jordanharrison?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 先决条件

1.  编程基础知识；最好是 JavaScript。
2.  [系统中安装的 Node.js](https://nodejs.org/en/download/) 。

# 创建您的项目

创建一个新文件夹，并以您的项目命名。用这个路径打开你的终端并运行命令`npm init -y` —这将创建一个带有默认值的`package.json`文件。

```
~> mkdir my-project
~> cd my-project
my-project> npm init -y
my-project> ls
package.json
```

> `package.json`文件包含您的 JavaScript 项目的配置。它将存储依赖项的名称和版本，以及其他信息。

说到依赖性…

# 安装快速依赖关系

Express.js 是一个可以在 Node 中使用以加速开发的库。在项目文件夹中运行命令

```
npm install --save expressor simply,
npm i express
```

**注意:**您现在会看到一个文件夹`node_modules`。该文件夹包含应用程序中所需的所有依赖项。但是你只装了一个，为什么看到这么多？是因为 Express 本身需要一些依赖关系，这些依赖关系需要其他依赖关系。因此，这整个依赖链是一次性安装的。

# 开始编写服务器

一旦安装了 express 依赖项，就创建一个新的 JavaScript 脚本文件，并随意命名(我喜欢称它为`server.js`)。为了在我们的项目中使用 express，我们需要使用`require`函数*导入*它。

```
const express = require("express")
```

这并不启动服务器本身，我们需要构建应用程序。我们可以通过以下方式做到这一点:

```
const express = require("express")
const app = express()
```

我们现在需要处理`app`对象来启动我们的服务器。

```
app.listen(3000, () => {
  console.log("Server has started! Open [http://localhost:3000](http://localhost:3000)")
})
```

好极了。我们已经加载了这个服务器。但是等等，它是做什么的？没什么。

# 编写 GET /函数

如果你熟悉 HTTP 函数，`GET`是一个 HTTP 方法，用来获取一些数据。在这里，我们想获得主页(即 URL '/')。

```
app.get('/', async (req, res) => {
  res.send("Hello World!")
})
```

# 最后启动服务器

所以你建立了你的服务器，但是你需要启动它。它就像运行一个命令一样简单(我想知道到目前为止我们一直在做什么):

```
node server.js
```

其中`server.js`是包含代码的文件的名称。

# 结论

```
const express = require("express")
const app = express()app.get('/', async (req, res) => {
  res.send("Hello World!")
})app.listen(3000, () => {
  console.log("Server has started! Open [http://localhost:3000](http://localhost:3000)")
})
```

启动后端服务器并没有您想象的那么复杂。随着您开始广泛地学习后端，您的项目将在规模和复杂性上增长。

我希望你喜欢读我的文章，并学到了一些东西。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```