# ES6 模块及应用。

> 原文：<https://medium.com/codex/es6-modules-and-application-34a40365cd43?source=collection_archive---------20----------------------->

![](img/742ec1f1c627cb7caee1881b0eee76d4.png)

保罗·埃施-洛朗在 [Unsplash](https://unsplash.com/) 上拍摄的照片

JavaScript 中的模块已经存在很长时间了，但最初是在库中实现的。例如，我们如何导入一个 React 组件，并在不同的组件中使用它，而不需要从头开始重写函数或类。模块帮助我们避免重复编写相同的逻辑。它还有助于分离关注点；你可以有一个单独的模块来添加数字，或者从 API 获取数据。这样，你就能准确地知道所有东西的归属。想象一下，如果谷歌的所有代码都在一个文件中，每次团队必须修复搜索栏中的某些内容时，他们必须滚动十亿行代码才能找到特定的部分，这难道不令人兴奋吗？模块帮助我们分离每个功能，从而解决每个问题或单独扩展某些功能。

现在有一个好消息(有点旧消息)，随着 ECMAScript 2015 的发布，模块被引入到普通 JavaScript 中，通常被称为 ES6。该版本附带了许多特性，将普通 JS 提升到了一个全新的水平。这些特性包括**箭头函数、剩余**和**展开、析构、类、let** 和**常量、模块**等。本文将只关注模块及其在普通 JavaScript 中的应用。

# **ES6 模块的应用**

首先，我们将创建包含主脚本和模块的文件夹。一个模块将用于加法，而另一个模块将用于减法。

这是文件夹结构的样子:

> index.html
> script . js
> my modules/
> add . js
> sub . js
> 
> 有些资源可能会使用。mjs 扩展来注释模块文件，但是我们将使用一种不同的方法，在我们的脚本标记中包含 type="module"。如下所示:

```
<!-- index.html -->

<!DOCTYPE html>
<html lang="en"><head>
    <title>ES6 Modules</title>
</head><body> <script type="module" src="script.js"></script></body>
```

现在让我们创建我们的函数—一个用于加法，另一个用于减法:

加法函数:

```
//add.js

function add(a, b){
    return a + b;
}
```

减法函数:

```
//sub.js

function sub(a, b){
    return a - b;
}
```

## **出口和进口**

我们所做的就是创建带有函数的常规脚本。那么我们如何在其他脚本中使用这些函数呢？这是使用**导出**和**导入**关键字完成的。

换句话说，要访问一个模块的功能，必须从它被创建的地方导出它，然后导入到它将被使用的任何文件中。

让我们导出我们的 add.js:

```
//add.js

export function add(a, b){
    return a + b;
}
```

现在，通过在我们的函数前添加**导出**，它使得脚本可以导入到其他可以使用它的函数的脚本中。

对 sub.js 执行相同的过程:

```
//sub.js

export function sub(a, b){
    return a - b;
}
```

既然我们已经导出了脚本，那么让我们将它们导入到我们的主脚本中并加以利用。

```
//script.js

import { add } from "./myModules/add.js";
import { sub } from "./myModules/sub.js"

console.log(add(6, 4)); // 10

console.log(sub(6, 4));  // 2
```

语法很容易理解。以 **import** 关键字开始，然后是嵌套在花括号内的被导入函数的名称，最后是导入脚本的路径。

注意我们如何使用 add 和 sub 函数，而没有从头开始创建一个带有新参数的函数？这就是 ES6 模块的强大之处，我们的脚本现在可以在任何地方重用，只要它在创建时被导出，在使用前被导入。这些脚本现在可以导入到我们希望使用它们的任何其他脚本中。这还通过创建一个脚本作为所有其他脚本的入口点，消除了在 HTML 文件中使用多个脚本标记的情况。

当导入不同的内容时，您同样可以更改函数的名称。例如，您想要导入子函数，但您更愿意称它为“minus”。这可以通过在导入时在函数的原始名称后添加“as”来实现。下面的例子:

```
//script.js

import { sub as minus } from "./myModules/sub.js"

console.log(minus(6, 4));  // 2
```

感谢您的阅读；我希望这篇文章值得您花费时间。如果有帮助，请留下掌声、分享和评论:)

[你也可以订阅，把我的文章直接发到你的邮箱里。](/subscribe/@ihechivinabba)