# MSYS2 构建过程让你抓狂？

> 原文：<https://medium.com/codex/msys2-build-processes-driving-you-crazy-83d4a23bf448?source=collection_archive---------4----------------------->

## 你并不孤单。

![](img/e08b7e06d9bcd6b882573c0631123919.png)

[环境元素](https://elements.envato.com/stressed-computer-programmer-in-front-of-computer-NE3XYMG)

在工作中，我的任务是为多种平台开发特定的加密货币钱包。其中包括 Windows，我们不使用 Visual Studio 进行构建，而是将 CMake 与 MSYS2 一起使用，因此我们不必仅为特定于 Windows 的边缘情况重写整个代码库。

不过，只有一个问题。当我被分配到这个项目时，代码库就是不能根据构建说明通过 Windows 上的 CMake 配置过程。起初似乎没有任何问题，但后来，有一些错误转移了我的调试。

最终，我成功地把它推过了 CMake。我是这样做的。

# 关于 MSYS2 的一些背景信息

你实际上并没有在 MSYS2 内部构建应用程序。你在 MinGW 内部建造它们——那是因为 MSYS2 只是一个外壳。没错，MSYS2 只给你提供了一个 bash 式的 shell，其余的 GNU 工具都是 MinGW 提供的。

特别是，所有的构建工具只有在运行 MinGW 版本的 MSYS2 shell 时才可用。所以每个不使用 Visual Studio 的 Windows 项目的构建指令都会要求你在 MSYS2 下构建。

MSYS2 和 MinGW 使用`pacman`包管理器来安装软件。有两套包(嗯，实际上不止这些，但我没有把 32 位或通用 C 运行时或跨平台包算在内)。`mingw-w64-x86_64-*`包安装在 MinGW 的“名称空间”，不合格的(没有疯狂前缀)包安装在 MSYS2 的名称空间。

从 MinGW 程序中可以看到不合格的包，但显然，从 MSYS2 中看不到`mingw*`包，但如果这是错误的，请在评论中告诉我，因为我是从反复试验中发现的。

# 问题是

```
CMake Error: File <REDACTED>/external/unbound/win32pthread-NOTFOUND does not exist.-- Could NOT find Readline (missing: Readline_INCLUDE_DIR)Doxygen: graphviz not found - graphs disabled
```

这些错误中有你觉得熟悉的吗？显然，谷歌返回了一些关于它们的搜索结果，但并没有真正为它们中的任何一个提供答案。

其中一些错误可以通过安装相关的`mingw-*`包来解决。这是 Doxygen 和 graphviz 没有被发现的情况。其他的，比如前两个，更加微妙，不能仅仅通过安装包来解决——在 CMake 文件中还需要做更多的工作。

只有 pthreads 错误是致命的，但它们都同样令人讨厌。让我们先从 Readline 错误开始。

首先，确保已经安装了`mingw-w64-x86_64-readline`。Doxygen 和 graphviz 包也是如此。MinGW 包是必须的，而不是不合格的 MSYS2 包。

您的 Readline 包配置可能如下所示:

调用 find_package，然后检查是否找到了 Readline(以及在没有 Readline 支持的情况下构建的诊断打印)。

这里的问题是，find_package 永远找不到 Readline 的路径。这很奇怪，有两个原因。第一，find_package 在查找其他包时没有问题，第二，Readline 库已经安装在 MinGW 内部。那么问题会是什么呢？

在做了一些检查之后，我发现 find_package 确实找到了 Readline **库**，但是没有找到 include 路径。显然，这是因为它被告知要查找一个特定的文件名(readline/readline.h ),该文件名中有一个正斜杠作为路径分隔符。

MinGW 程序不理解 UNIX 路径，因为它们被编程为只理解 Windows 路径。这是为了让它们在 Windows 下工作。

更糟糕的是，MSYS 只支持 UNIX 路径，而不支持 MinGW 程序使用的 Windows 路径。

这在 CMake 中造成了一些混乱，因为 find_package 函数最终只调用了 find(1)程序，这是一个 MinGW 程序，只理解 Windows 路径。

更糟糕的是，不管你安装的是 MSYS 还是 MinGW 版本的 CMake，它都只接受 UNIX 路径作为文件规范。这导致您必须在同一个配置文件中混合使用 Windows 和 UNIX 路径，这会变得非常混乱。

我本可以这样做，并修改负责查找 Readline 包的文件，但由于这不可移植，我决定实现一个回退条件，只针对 MinGW。

正如你所看到的，在固定代码中有一个特殊的条件，检测 find_package 找不到 Readline 的情况，以及程序是否在 MinGW 下构建。在这种情况下，我只是发出一个警告，硬编码 Readline 的路径，并继续处理 CMake 文件的其余部分。

这个特例还检查了 32 位和 64 位 MinGW。

正如我前面提到的，pthreads 错误是停止 CMake 执行的错误，而不是其他任何错误。在这种情况下，它抱怨在 Windows 下找不到 pthread **库**。

这可能会令人困惑，因为 Windows 不使用 pthreads —它使用自己的特定于操作系统的线程库。所以 MinGW 通过发布 pthreads 来模仿 Windows 上的 pthreads。专门用于 Windows 的 dll 库。

这个错误基本上是说它找不到那个文件。

首先，确保安装了`mingw-w64-x86_64-winpthreads-git`——这将安装 pthreads 开发文件，还将拉取`*-libwinpthread`包，该包提供了需要找到的/mingw64(或 32)/bin/libwinpthread-1.dll 库。

这里，我们必须使用 Windows 路径来指定这个文件，因为在这个搜索之后立即发生的(未示出的)复制功能将不能与 UNIX 路径一起工作。

结果如下所示，并带有警告和 64 位检查:

现在，您应该能够构建您的代码库，而不会显示这些错误，并且编译器将能够自动找到这些文件。