# 我如何开始编写一种新的编程语言

> 原文：<https://medium.com/codex/how-i-started-to-write-a-new-programming-language-51db6382c799?source=collection_archive---------20----------------------->

![](img/9a1e7f7cb5888926d6637c87b3c3b9db.png)

> 古往今来的所有统治者都试图将错误的世界观强加给他们的追随者。——《1984》，乔治·奥威尔

我从事 [JavaScript 代码转换器](https://github.com/coderaiser/putout)已经有几年了，很久以前，我看到了一个关于向 JavaScript 添加新关键字的新问题。这是一个奇怪的想法，因为我用 JavaScript 写了大约十年，但我试图帮助写我的人，并开始从一个不同的角度研究我非常熟悉和感兴趣的领域。

我使用了各种 JavaScript 解析器，并为 [Babel](https://github.com/babel/babel/pulls?q=is%3Apr+author%3Acoderaiser+is%3Aclosed) 和 [Recast](https://github.com/coderaiser/recast) 贡献了一点(我甚至有自己的 fork！).所以我想:为什么不呢，那很有意思！

我开始阅读 acorn 代码库，阅读一些 stackoverflow，突然事情变得如此简单，我无法想象。这主要是因为 [acorn](https://github.com/acornjs/acorn) 支持插件，这使得事情变得容易多了。

所以我首先添加的是一个[新关键字](https://github.com/coderaiser/recast) `[fn](https://github.com/coderaiser/recast).`

并且这样的结构变得可解析到`FunctionDeclaration`:

```
fn hello() {
    return 'world'
}
```

然后从这期的谈话中，一切都开始了，我有了一个新的目标要实现:[迅捷卫士](/@coderaiser/lets-add-swift-guards-to-javascript-42767b99a3ba)。我从来没有写过 Swift，但我认为那是包含很多好想法的透视语言，就像 Rust 一样。

# 下一步是什么？

我的下一步是创建一个存放测试的仓库，让事情变得更加稳定。所以让我来介绍一下: [Goldstein](https://github.com/coderaiser/goldstein) ，没有限制的 JavaScript🤫。

如果你对梦想中的编程语言有任何想法，只需创建一个问题，我们将决定如何实现你的梦想！

# 支持什么？

现在，正如我之前所说的，我们支持`fn`、`guards`和一些更有趣的东西…

# 尝试表达式

有很多关于尝试表达的讨论。我长久以来想要的是能够简化这个又大又丑的陈述:

```
**try** {
    hello('world');
} **catch** (error) {}
```

类似于:

```
**const** [error, result] = tryCatch(hello, 'world');
```

这可以通过`[try-catch](https://github.com/coderaiser/try-catch)`库来实现。它很好，被很多人使用。我想要的实际上是一个语法结构。原来如此！

```
**const** [error, result] = **try** hello('world');
```

如果你想使用`await`，你可以！

```
**const** [error, result] = **try** **await** hello(‘world’);
```

这将被转换为使用`try-catch`库，该库将在 **CLI 的帮助下捆绑成一个文件。**

# **命令行界面**

没错。您可以安装`goldstein`并使用它的命令行界面，如下所示:

```
npm i goldstein -g
```

安装后我们有一个`gs`二进制:

```
gs hello.gs
```

将代码编译成`hello.js`文件，可以在任何 JavaScript 环境中使用。

# 美国石油学会怎么了？

API 也支持！

```
**import** {compile} **from** 'goldstein';compile(`
    const [error, result] = safe hello('world');
`);
// returns
`
    import tryCatch from 'try-catch';
    const [error, result] = tryCatch(hello, 'world');
`
```

# 结论

今天到此为止！我想欢迎任何贡献和评论与想法的语法你的梦想。

感谢阅读，敬请关注！请订阅，我需要更多的追随者😏。