# 最小反应+网络包 4 +巴别塔 7 项目设置初学者指南

> 原文：<https://medium.com/codex/beginners-guide-to-minimal-react-webpack-4-babel-7-project-setup-9d2c9921e7b6?source=collection_archive---------8----------------------->

![](img/7b967d1c4bbd550bf742b32ee6393af3.png)

React 已经成为近几年开发者首选的前端库。学习和实践任何编程语言工具的最好方法是用它来构建东西--为此，我们需要建立一个编码平台。开始这样一个项目的最快方法是用`create-react-app (CRA)`引导它。之所以会有这样的事情存在，是因为现代 Javascript 工具已经变成了一个不可思议的巨大空间(这里滑稽地解释)。像 CRA 这样的初学者工具包帮助我们避免在这种情况下导航，而专注于编写有趣的东西。然而，对于开发人员来说，了解配置项目的一些关键部分仍然是至关重要的。我们将在这个最小化 React 项目设置的分步指南中了解这一点。

在本教程中，我们将学习以下内容:

*   Webpack 的基本概念，并利用它来建立我们的项目
*   什么是巴别塔，它有什么作用，我们为什么需要它
*   如何将 React 集成到我们的项目中
*   如何使热模块重新加载

让我们从学习工具的基础开始，我们今天将使用这些工具来配置我们的第一个项目。

# 反应 JS

React 是一个 javascript 库，它带来了构建用户界面的范式转变。由脸书开发和维护，它通过小的、可重复使用的组件来观察世界。想象一套乐高玩具，我们把积木放在一起，创造出一个结构。类似地，React 组件本身并不意味着很多，但是当通过有经验的开发人员的手系统地组装时，它可以成为构建美丽而复杂的用户体验的强大工具。

## 关键特征

*   简单、可重用的组件
*   声明性的。代码可读性更强，也更容易调试。
*   单向数据绑定。数据严格地从父组件流向子组件
*   虚拟 DOM。算法计算当前和更新的 DOM 之间的“差异”(通过用户交互、从 API 调用接收的数据等),以仅重新呈现视图中需要更新的部分。
*   表演

# 网络包

Webpack 的官方网站称自己是一个静态模块捆绑商——但我认为它低估了自己。是的，从最简单的意义上来说，就是*，但它能做的远不止这些。Webpack 是一个非常可配置的工具，它打包了所有的前端代码(js、CSS、模板引擎、静态资产),并创建了浏览器可以下载的包，为您的应用程序提供功能。*

## 关键特征

*   模块捆扎机。将前端代码打包成包供浏览器下载。
*   代码分割。这些包可以根据页面、功能等进一步分解，以便浏览器下载尽可能少的代码来运行应用程序。
*   树在晃动。如果这样配置的话，它不会将死的/不可访问的代码打包到包中。
*   加载器和插件。允许我们扩展 Webpack 提供的现成功能。
*   **热模块更换** (HMR)。在开发过程中，Webpack 可以动态地换出 JS 或 CSS 模块来更新 UI，而无需刷新页面。
*   环境特定的配置。例如，我们不希望在开发环境中减少代码，但是我们绝对希望在生产环境中这样做。

# 巴比伦式的城市

Babel 是一个 JavaScript trans piler--它将现代 JS 代码编译成旧版本，以便旧版本的浏览器可以执行它们。大多数浏览器的最新版本可以解析较新的 JS 特性，但传统浏览器不能。我们不希望我们的应用程序在这些浏览器上运行失败，而*和*正是巴别塔拯救世界的地方。

## 关键特征

*   将现代 JS 代码编译成旧版本的 JS，以便旧版本的浏览器可以执行。
*   允许插件预设在其现成功能的基础上进行扩展。

好了，现在我们已经了解了将要使用的工具的核心概念，让我们来写一些代码吧！

**请注意:**这里值得一提的是，你机器中的*节点*版本必须是 10.15.3 以上。通过在终端中键入`node -v`进行检查。如果你需要更新或者根本没有安装节点，你可以在这里得到安装程序

## 第一步:

打开您的终端并导航到您想要创建存储库的目录。

```
cd ~/Desktop/projects mkdir minimal-react-webpack-babel && cd $_ mkdir -p src
```

这将创建一个名为“minimal-react-webpack-babel”的目录，我们的代码将存放在其中，并在其中创建一个“src”子目录。

## 第二步:

此时，我们需要初始化 **package.json**

```
npm init -y
```

这将一次性创建文件，大多数字段为空(如描述和作者)，其他字段设置为默认值(如版本设置为 1.0.0)。您可以稍后回来适当地填写它们。

现在 **package.json** 应该是这样的

```
{
  "name": "minimal-react-webpack-babel",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

## 第三步:

让我们在我们的项目中安装 **webpack** 和 **webpack-cli** 。

```
npm install webpack webpack-cli --save-dev
```

Webpack 的 Pre *v4.0.0* 以前需要寻找一个配置文件来运行，但是从版本 4.0.0 开始，Webpack 可以将包打包。

在后面的教程中，我计划演示一个更高级的 Webpack 设置(特定于环境的配置、linters、测试运行程序等),它将建立在我们今天所学的基础之上。我们暂时让它保持最基本的状态。

## 第四步:

安装 **react** 和 **react-dom** 的时间。

```
npm install react react-dom
```

React 应该作为常规依赖项安装，而不是作为`devDependencies`安装，因为我们需要 react 在应用程序运行时可用，并打包到应用程序的产品包中。

好吧，又快又简单！我们继续吧。

## 第五步:

我们必须在这里安装 **Babel** ，以及其他必要的预设。

```
npm install @babel/core babel-loader @babel/preset-env @babel/preset-react --save-dev
```

**解释** : React 是用老式浏览器无法执行的现代 ES6 特性编写的。因此，我们需要以某种方式将现代语法转换成 ES5。Webpack 不能执行这个操作--这就是 Babel 填补空白的地方。这个过程叫做*传输*。

我们使用上面的命令安装了三个依赖项:

*   【babel 和 webpack 的接口。它允许他们相互合作，产生最终的包。
*   `babel/preset-env`:负责将 ES6(或以上)转 ES5 的预置。
*   `babel/preset-react`:负责将 JSX 编译成正规 JS 的礼物。可以放弃安装这个依赖项，但是这样我们将无法使用 JSX 编写我们的 React 组件。

很好，现在我们明白了这一点，让我们配置*巴贝尔*使用我们刚刚安装的预置。首先，我们需要在项目的根目录下创建一个名为`.babelrc`的文件——这将包含配置规则。

```
touch .babelrc
```

这将是一个隐藏文件。使用以下方式打开文件:

```
open -e .babelrc
```

…添加以下行并点击保存:

```
{
  "presets": ["[@babel/preset-env](http://twitter.com/babel/preset-env)", "[@babel/preset-react](http://twitter.com/babel/preset-react)"]
}
```

## 第六步:

现在让我们安装一个*开发服务器*。从技术上来说，没有开发服务器也是可能的，但是这意味着每次我们对项目进行代码修改时，我们都必须构建我们的项目。这不是我们想要生活的世界--我们希望我们的应用程序在我们保存代码更改后立即更新。这个过程称为**热模块更换**。除了安装 dev-server 之外，对 webpack 配置稍作调整就可以为我们启用这个特性。

```
npm install webpack-dev-server --save-dev
```

现在，要让 webpack *构建*我们的代码，我们需要在 **package.json** 的`scripts`属性中添加一个脚本。这将在`production`模式下编译我们的代码。

我们还将添加一个 *start* 脚本，该脚本将启动我们的开发服务器并启用一个无缝的开发环境。

```
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "build": "webpack --mode production",
  "start": "webpack-dev-server --open --mode development"
},
```

## 第七步:

我们需要在项目中安装的最后一个依赖项是`html-webpack-plugin`和`html-loader`。

```
npm install html-webpack-plugin html-loader --save-dev
```

*   [html-webpack-plugin](https://webpack.js.org/plugins/html-webpack-plugin/) 将从我们将要编写的 React 组件中生成 html。
*   html 加载器将 HTML 导出为字符串*，如果我们要求的话*可以最小化它。

## 第八步:

我们完成了依赖项的安装！！我们现在需要做的是创建一个 HTML 文件，React 将在其中装载我们的应用程序。让我们称它为`index.html`，并把它放在我们之前创建的`src`目录中。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Minimal React + Webpack 4 + Babel 7 project setup</title>
</head>
<body>
<div id="app-container"></div>
</body>
</html>
```

## 第九步:

此时，我们将向 **webpack** 添加最少的配置规则。为此，我们将创建一个名为`webpack.config.js`的文件，并将所有的规则放在那里。在未来的教程中，我们将在今天所学的基础上，为开发和生产环境创建单独的配置文件。这将允许我们分离特定于环境的规则，提高可伸缩性和可维护性。

让我们在项目根目录下创建该文件

```
touch webpack.config.js
```

…并在其中添加以下几行:

```
const HtmlWebPackPlugin = require("html-webpack-plugin");module.exports = {
    devServer: {
        contentBase: './dist',
        hot: true
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader"
                }
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader"
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: "./src/index.html",
            filename: "./index.html"
        })
    ]
};
```

**解释**:这些行告诉 **webpack** 执行以下操作:

*   *devServer* 包含服务器实例的配置规则，我们将使用 dev-server 运行该服务器实例来托管我们的应用程序。`hot: true`启用热模块更换。
*   通过 **babel-loader** 管道*所有扩展名为`.js`或`.jsx`的*文件，除了`node_modules`目录中的文件。
*   使用我们在上一步中安装的 HTML 插件和加载器，从 React 组件和前端打包代码包*中生成 HTML，并将包*注入 HTML 中的`<script/>`标签。

## 第十步:

我们已经到达了设置过程的末尾——我们现在准备编写一些 React！！

让我们在`src`内部创建一个*组件*目录，编写一个简单的`App.js`组件，打印“Hello World”。

```
import React from 'react';
import ReactDOM from 'react-dom';const App = () => {
    return (
        <div>Hello World</div>
    )
};export default App;
```

我们还需要在`src`目录中创建`index.js`——并导入我们刚刚编写的组件。

```
import React from 'react';
import ReactDOM from "react-dom";
import App from './components/App.js';const $container = document.getElementById("app-container");
$container ? ReactDOM.render(<App />, $container) : false;
```

这个过程已经完成了——我们自己已经有了一个使用 babel 和 webpack 的 React 项目！！现在，如果我们运行`npm run start`，webpack 将启动开发服务器并在浏览器窗口中打开我们的应用程序。此外，由于我们已经启用了热模块替换，如果我们对应用程序进行代码更改(编辑现有的“hello world”组件，添加新组件等)，我们将立即在浏览器中看到我们的应用程序更新！

完成的回购是[这里的](https://github.com/all-things-javascript/minimal-react-webpack-babel)

## 结论

毫无疑问，`create-react-app`是建立一个 react 项目最快捷最方便的方式(目前)。然而，了解现代 javascript 工具是如何工作的——以及我们如何将这些工具拼凑在一起，从零开始建立一个前端项目，这是一项非常有价值的技能。

我希望这个循序渐进的指南能帮助你理解其中的一些内容。感谢您的阅读，敬请期待更多内容！！

这篇文章最初发表在我的博客上。它有语法高亮！！

*原发布于*[*https://nasidulislam . hashnode . dev*](https://nasidulislam.hashnode.dev/beginners-guide-to-minimal-react-webpack-4-babel-7-project-setup)*。*