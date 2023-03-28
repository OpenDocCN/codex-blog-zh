# 轻松地从 Kotlin 脚本或应用程序运行 shell 命令

> 原文：<https://medium.com/codex/run-shell-commands-from-a-kotlin-script-or-application-with-ease-e5764a6c7cff?source=collection_archive---------3----------------------->

![](img/f2997ba9e6c7dad4a2ade0fc20135369.png)

兰德尔·鲁伊斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

几天前，我在 Kotlin 应用程序中运行 git 命令时遇到了一个问题。首先，我使用 jGit api 直接在我的代码中运行 Git。问题是，我需要自己处理所有的认证过程，不管 ssh 密钥或密码是否已经在我的机器上定义了。这意味着每次有人使用我的工具时，我们都需要读入 git 帐户的密码和用户名。我发现获得密码作为用户输入的唯一方法是通过 console.readPassword()，每次我尝试时这个方法都会抛出一个错误。

需要一个更好的解决方案……而它就在那里。[乌龟](https://github.com/lordcodes/turtle)

# 什么是乌龟

Turtle 简化了从 Kotlin(或 Java)代码运行外部命令和进程的过程。它捆绑了一系列内置功能，如打开 MacOS 应用程序和处理 Git。在 Kotlin 脚本、命令行应用程序和 Gradle 任务中轻松运行 shell 命令尤其有用。

# 特征

## ▶︎用最少的样板文件运行 shell 命令

只需指定 comamnd 及其参数，就可以轻松运行和检索输出。

## ▶︎调用任何内置的 shell 命令

提供了各种命令，比如创建 Git 提交和打开文件。

## ▶︎使用函数语法运行一系列命令

指定在功能块中运行的命令序列。

## ▶︎捕获错误退出代码并输出

当命令产生错误时，退出代码和错误输出将作为异常抛出。

如您所见，这是我直接调用 git cli 并从我的 Kotlin 程序中运行这些命令的完美方式。这样，我不需要定义 jGit 的认证部分，因为这都是由 Git 本身在您的本地设备上处理的。

# 装置

Turtle 在安装过程中非常简单。只需将 gradle 依赖项添加到 build.gradle.kts 文件中，我们就可以开始了。

## ▶︎·格雷尔·科特林 DSL

```
dependencies {
  implementation("com.lordcodes.turtle:turtle:0.5.0")
}
```

## ▶︎ Gradle Groovy DSL

```
dependencies {
  implementation 'com.lordcodes.turtle:turtle:0.5.0'
}
```

请确保使用最新版本的 turtle，因为一些旧版本的问题在最新版本中得到了解决。

# 如何使用它

要运行自定义命令，只需运行 shellRun()并提供命令和一些参数(如果需要的话)。

可以向列表中添加多个参数。请记住，如果应该显示命令的输出以及与 cmd 行的交互，请打印出命令的输出。

## 改变工作方向

预先定义命令的工作目录。没有必要一直用 cd 来做这件事。

您可以在命令的参数列表后添加 shellLocation。

## 运行一系列命令

它支持在 shell 块中运行多个 shell 命令，在 shell 块中还可以为所有 shell 命令设置 shellLocation。

## Git 命令

运行 git 命令有两个选项:

1.  使用 shellRun()命令，并像其他命令一样运行 git。
2.  使用 turtle 定义的 git 函数，享受它们的简单性。

一些可用的 git 命令:

当然，您也可以手动执行所有这些命令，但是这种方式最终会更干净、可读性更好。

## 处理文件

Turtle 还提供了一些处理文件的函数。除了你电脑上默认有的工具(nano，vim，mkdir…)，这个可以帮你处理文件。

尤其是 openApplication()和 createSymlink 函数，使得创建功能丰富的 Kotlin 脚本变得更加容易。

# 反射

## 什么进展顺利？

该工具的安装和使用非常方便。文件很好，也很容易理解。我能够很快地使用 git 命令，并且配置它们很容易。

## 有哪些需要改进的地方？

最大的问题是，我完全专注于 jGit，对 turtle 等其他解决方案视而不见。当我最终换成 turtle 的时候，一切都变得简单直接了。下一次，我会多告诉自己一些不同的解决方案，而不是把自己固定在一个特定的解决方案上。

我希望这篇文章是有趣的，对你有帮助😃