# asdf，每个程序员的必备

> 原文：<https://medium.com/codex/asdf-a-must-have-for-every-programmer-ad948f0302bd?source=collection_archive---------6----------------------->

## 一个运行时管理器来管理它们

![](img/365c1c85655baedfed7a1e967c218573.png)

## 那么什么是 asdf

嗯不是一个错别字😅。

asdf 是一个“单一工具”,能够管理程序员本地拥有的所有运行时和工具。NodeJS，NPM，Java，mvn，gradle，go，rust，deno…如果你知道，那么 asdf 会保护你。

目前，它以插件的形式支持超过 450 个运行时**和**工具**。**

**您可以在[回购页面](https://github.com/asdf-vm/asdf-plugins)自行查看**

## **那么我为什么要用呢？**

**Well 非常容易使用。
假设您想要管理 NodeJS 运行时。
**首先，您需要使用以下命令将插件添加到 asdf:****

```
asdf plugin add nodejs [https://github.com/asdf-vm/asdf-nodejs.git](https://github.com/asdf-vm/asdf-nodejs.git)
```

**这个命令将在你的机器上安装插件。**

****您可以使用这个命令**可视化 asdf 拥有的所有 NodeJS 版本**

```
asdf list-all nodejs
```

****现在安装你需要的版本，例如****

```
asdf install nodejs 14.8.0
```

**现在我们需要将它设置到**本地**终端**

```
asdf local nodejs 14.8.0
```

**或使用此命令对**全局**执行:**

```
asdf global nodejs 14.8.0
```

## **以后，要更改 NodeJS 版本，只需:**

**今后只需运行那些**中的**一个**两个**命令:**

```
asdf local nodejs 14.8.0
```

**或者**

```
asdf global nodejs 14.8.0
```

**更多信息，您可以查看[工具库](https://github.com/asdf-vm/asdf)文档。**

**让我知道你的想法。这只是另一个笨重的工具吗？**

**感谢您的阅读，一如既往，如果您喜欢，请留下一些掌声👏👏👏因为它对我帮助很大。**

**祝你愉快。**