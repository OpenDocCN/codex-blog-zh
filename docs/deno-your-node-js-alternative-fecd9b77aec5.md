# Deno:您的 Node.js 替代方案

> 原文：<https://medium.com/codex/deno-your-node-js-alternative-fecd9b77aec5?source=collection_archive---------11----------------------->

## 新的 JavaScript 运行时环境

![](img/bc73e2a128633d7a0db2b32344ba459b.png)

[来源](https://blog.bitsrc.io/what-is-deno-and-will-it-replace-nodejs-a13aa1734a74)

> 我们做错了——瑞安·达尔

是啊！Nodejs 的创始人瑞安·达尔(Ryan Dahl)对他的产品总体上并不满意。它帮助数百万 JavaScript 开发者在浏览器之外运行他们的代码；然而，他并没有停止，并试图为所有 JavaScript 爱好者(包括我，但我更喜欢 Kotlin)创造一个更好的运行时环境。

如果从 ***节点*** 中取前两个字母，放在最后，就得到了 ***Deno*** 。

```
function ryanDahl() {
  const str = "node";
  const first = str.substring(0, 2);
  const last = str.substring(2);
  console.log(last + first); // Outputs: deno
}
```

那么 Deno 推出什么比 Node.js 更好的呢？

# 内置的类型脚本支持

人人都爱打字稿。为了让您的节点项目支持 TypeScript，您需要配置您的项目并显式地处理`tsconfig.json`文件。您必须确保您的源文件夹和目标文件夹位于正确的位置。它使您的代码分成您的类型脚本和 JavaScript 部分。讨厌——谁想要那个？

Ryan Dahl 致力于 Deno，以允许 TypeScript 支持，使您能够专注于开发而不是 TypeScript 配置。

# 顶级 await/async 语法

在 JavaScript 或 TypeScript 中，您还在使用`then`和`catch`链接语法来执行异步任务吗？如果你的答案是肯定的，那么我不想告诉你，但是你的发展落后了十年。使用这种语法没有错；但是在我看来，async/await 语法更清晰，更容易理解。

Deno 具有顶级的 async 和 await 特性，这意味着使用 await 关键字不需要异步函数。您可以在任何需要的地方自由使用 await 关键字。

# 您不再需要依赖`node_modules`

你有没有因为 100 个项目占用的空间太大而感到恼火，仅仅是因为每个项目中都有`node_modules`文件夹？随着 Node 的日益流行，Ryan Dahl 看到了这个问题，并决定在构建 Deno 时与之对抗。

所有 Deno 依赖项都托管在 [DenoLand](https://deno.land) 上——它以它们的 [*标准库*](https://deno.land/std@0.144.0) 以及 [*第三方库*](https://deno.land/x) *为特色。*Deno 中的导入语法使用这些云模块来运行你的代码，不会在`node_modules`上浪费内存。

# 安全功能

节点缺乏安全性。你被允许执行某些应该被禁止的代码。Ryan Dahl 认为这是 Node 中的一个有害因素，他必须修复它。

默认情况下，Deno 应用程序是安全的，在执行文件时会提供权限。例如，您的应用程序**不能**读取您系统上的任何文件，除非执行命令通过了`--allow-read`标志。类似地，除非您传递了`--allow-net`标志，否则您不能创建 REST API。

# 清除器 REST API 代码

所以你正在开发一个更新的运行时环境。你应该使编码环境现代化，对吗？有许多像 [ABC](https://deno.land/x/abc@v1.3.3) 和 [Oak](https://deno.land/x/oak@v10.6.0) 这样的第三方库致力于构建一个更干净的 REST API。

这里我最喜欢的发展是路由链和它们各自的 HTTP 方法。

```
router.get('/', (ctx) => {
    ctx.response.body = 'Hello from Deno'
})
    .get('/rooms', getAllRooms)
    .get('/rooms/:id', getRoom)
    .post('/rooms', createRooms)
    .put('/rooms/:id', updateRoom)
    .delete('/rooms/:id', deleteRoom)// Code taken from [LogRocket blog](https://blog.logrocket.com/building-a-restful-api-in-deno-with-oak-and-mongodb/).
```

# 结论

这会取代 Node 吗？我觉得**是的**。然而，这种转变需要很多年才能发生。德诺只有几岁。2020 年 5 月，Deno 发布为生产就绪，然而，一些功能仍然是实验性的。Deno 是一个成长中的社区，值得关注。

尽管有缺点，Node 的粉丝群还是很大的。我很欣赏 Ryan 为构建这个令人惊叹的运行时环境所做的努力；但是就像其他技术一样，Deno 在行业中有需求只是时间问题。你怎么想呢?

我希望你喜欢我的文章，并学到了一些东西。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) Profile.
My [Portfolio](https://cybercoder-naj.github.io) Website.
```