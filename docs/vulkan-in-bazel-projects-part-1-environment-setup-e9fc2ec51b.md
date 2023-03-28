# Vulkan 项目中的 Bazel。第 1 部分—环境设置

> 原文：<https://medium.com/codex/vulkan-in-bazel-projects-part-1-environment-setup-e9fc2ec51b?source=collection_archive---------3----------------------->

## 通过 Bazel 构建系统运行使用 Vulkan 图形 API 的项目

![](img/9339a98d5e9c4a6911ff58c00a97d621.png)

照片由[哈维尔·米兰达](https://unsplash.com/photos/MrWOCGKFVDg)在 [Unsplash](https://unsplash.com/) 上拍摄

在这篇博文中，我们将看到如何为用 Bazel 构建的 Vulkan 项目设置环境。然后，在[第二部分](/codex/bazel-in-vulkan-projects-part-2-shaders-35cdeb0f4c67)中，我们将使用构建 GLSL 着色器和绘制三角形的项目。

**Vulkan —** 是 3D 图形和计算的开放标准，支持跨平台访问 GPU。它面向高性能实时 3D 图形应用，如视频游戏和交互式媒体。你可以从其[官方页面](https://www.vulkan.org/)了解更多关于 Vulkan 的信息。

**Bazel** —是一个开源构建系统，具有高级本地和分布式缓存、优化的依赖性分析和并行执行。它支持多种语言，包括 C++。你可以从其官方页面了解更多关于 Bazel 的信息。

我们将严格遵循 Vulkan 的[官方环境设置指南](https://vulkan-tutorial.com/Development_environment#page_MacOS)，但用 Bazel 构建一切。为此，我们将使用[**rules _ vulkan**](https://github.com/jadarve/rules_vulkan)**项目。**

**此外，我们将设置通常与 Vulkan 一起使用的工具，例如:**

*   ****GLM** —线性代数运算库。**
*   **GLFW —一个用于创建窗口的跨平台 API。**

**您可以使用下面的链接在 GitHub 上找到这篇博文的完整源代码。**

**[](https://github.com/Morfly/vulkan-bazel-samples) [## GitHub-Morfly/Vulkan-bazel-samples:一个为 Vulkan……

### 这是一个样例库，展示了如何设置一个开发 Vulkan 应用程序的环境

github.com](https://github.com/Morfly/vulkan-bazel-samples)** 

# **先决条件**

## **安装 Bazel**

**这篇博文假设您的机器上安装了一个 Bazel(或者它的包装器 Bazelisk)。访问官方文档了解如何做。**

## **正在下载 Vulkan SDK**

**在进行任何 Bazel 设置之前，我们必须下载并安装 Vulkan SDK 到我们的机器上。这一步和你遵循官方的 Vulkan 文档是完全一样的。**

*   ****macOS。**只遵循[MAC OS 官方 Vulkan 教程](https://vulkan-tutorial.com/Development_environment#page_Vulkan-SDK-2)中的 *Vulkan SDK* 部分。**
*   ****Linux。**只遵循[官方 Linux 漏洞教程](https://vulkan-tutorial.com/Development_environment#page_Vulkan-Packages)中的*漏洞包*部分。**
*   ****Windows。**只遵循[Windows 官方 Vulkan 教程](https://vulkan-tutorial.com/Development_environment#page_Vulkan-SDK)中的 *Vulkan SDK* 部分。**

# **设置项目**

**通常对于 Bazel 项目，我们必须初始化项目工作区。为此，在项目的根目录下(姑且称之为`vulkan-bazel-samples`)我们需要创建一个名为`WORKSPACE`的文件。**

```
vulkan-bazel-samples
└── **WORKSPACE**
```

## **为 Vulkan 配置 Bazel**

**首先，我们需要为 Bazel 项目添加 Vulkan 支持。为此，我们将使用 **rules_vulkan** repo。打开`WORKSPACE`文件，用下面的代码填充它。**

****工作区**文件内容。第 1 部分—在项目中配置 **rules_vulkan****

**在这里，我们获取 **rules_vulkan** 作为`git_repository`，并使用`vulkan_repositories`宏在项目中配置它。**

**我们将在后面的博文中看到如何使用它。但是，在此之前，我们需要配置额外的通常在 Vulkan 项目中使用的第三方库。**

## ****配置 GLM****

**GLM 是一个线性代数运算的库。**

**首先，我们需要让它在我们的项目中可用。为此，我们将使用一个`http_archive`构建规则，它将从 GitHub 档案中下载源代码。**

****工作区**文件内容。第 2 部分—加载 GLM 库**

**这样，我们在项目中获取库源代码，我们需要配置一个`BUILD`文件来告诉 Bazel 如何构建它。我们将这个文件的路径作为上面的`build_file`参数传递。这个文件将被*放在*被获取的库的根目录中。**

**显然，我们需要创建这个`BUILD`文件。我们需要将它放在`third_party/glm`文件夹下，并将其命名为`glm.BUILD`。以下是它的内容。**

****第三方/glm/glm。构建**文件内容**

**这里没有什么特别的，因为我们只是告诉 Bazel 从哪里获得库的源文件。**

**完成上述操作后，GLM 图书馆将作为外部工作空间`@glm`。为了便于使用，我们可以为它创建一个简单的别名。为此，创建一个`third_party/glm/BUILD`文件并用下面的内容填充它。**

****第三方/glm/BUILD** 文件内容**

**下面是项目到目前为止的文件结构。**

```
vulkan-bazel-samples
├── **third_party**
│   └── **glm**
│       ├── **glm.BUILD**
│       └── **BUILD**
│
└── WORKSPACE
```

**接下来，我们将在项目中包含一个 GLFW 库。**

## ****配置 GLFW****

**GLFW 是一个用于创建窗口的跨平台 API。**

**将其添加到项目中的算法类似于 GLM。然而，我们需要执行更复杂的配置。将下面的代码添加到`WORKSPACE`文件中。**

****工作区**文件内容。第 3 部分—加载 GLFW 库**

**与 GLM 类似，我们需要为 GLFW 库指定一个`BUILD`文件。为此，在`third_party/glfw`目录下创建一个名为`glfw.BUILD`的文件。接下来，我们需要用内容填充它。**

**首先，对于每个受支持的操作系统，我们需要选择正确的实现源文件、`define`语句和链接器标志。为了节省空间，下面的代码片段定义了相应的变量，但省略了它们的值。然而，这个文件的完整代码可以在 [GitHub](https://github.com/Morfly/vulkan-bazel-samples/blob/main/third_party/glfw/glfw.BUILD) 上找到。**

**第三方/glfw/glfw。构建文件内容。第一部分**

**接下来，我们需要定义一个`cc_library`目标，为 Linux 或 Windows 操作系统构建这些源代码。**

**第三方/glfw/glfw。构建文件内容。第二部分**

**类似地，要为 macOS 构建 GLFW 源代码，我们需要定义一个`objc_library`目标。**

**第三方/glfw/glfw。构建文件内容。第三部分**

**最后，我们正在定义一个公共 Bazel 目标，它将 GLFW API 头与我们之前定义的实现链接起来。**

**第三方/glfw/glfw。构建文件内容。第四部分**

**此外，创建一个`third_party/glfw/BUILD`文件，为外部`@glfw`存储库定义简单的别名。**

****第三方/glfw/BUILD** 文件内容**

**这是我们到目前为止更新的文件结构。**

```
vulkan-bazel-samples
├── **third_party** │├── glm
│   │
│   └── **glfw**
│       ├── **glfw.BUILD**
│       └── **BUILD**
│
└── WORKSPACE
```

# **编写和构建 Vulkan 代码**

**现在，我们准备添加一些使用 Vulkan API 的 C++代码。它不会做任何花哨的事情，只是显示一个空窗口并打印 Vulkan 扩展的列表。这足以证明项目配置正确。**

**在`env_setup`目录下创建一个`main.cpp`文件。用下面的代码填充，代码取自官方 [Vulkan 教程](https://vulkan-tutorial.com/Development_environment)。**

****env_setup/main.cpp** 文件内容**

**接下来，我们需要在`env_setup`目录下为我们的源代码添加一个`BUILD`文件。**

****环境 _ 设置/构建**文件内容**

**我们正在定义一个使用我们之前配置的`glfw`和`glm`库的`env_setup`二进制目标。此外，它还依赖于来自`@rules_vulkan`外部工作空间的`vulkan_cc_library` 。这就是我们如何使 Vulkan 支持这个目标。**

**下面您可以找到更新的项目结构。**

```
vulkan-bazel-samples
├── **env_setup**
│   ├── **main.cpp**
│   └── **BUILD** │
├── third_party
└── WORKSPACE
```

## **运行代码**

**最后，我们准备与 Bazel 一起构建和运行这个项目。为此，请在终端中运行以下命令。**

```
bazel run //env_setup
```

**如果一切正常，您应该会在控制台中看到一个空窗口和一堆 Vulkan 扩展。**

# **结论**

**就是这样！在这篇博文中，我们看到了如何建立一个使用 Vulkan graphics API 的项目，并使用 Bazel 使用 **rules_vulkan** repo 来构建它。此外，我们还看到了如何将 GLM 和 GLFW 库添加到这样一个项目中，当使用图形 API 时，它们是不可或缺的伙伴。**

**在第二部分[中，我们将看到如何用 Bazel 构建 GLSL 着色器，并应用这些知识来运行一个绘制三角形的项目。](/codex/bazel-in-vulkan-projects-part-2-shaders-35cdeb0f4c67)**

**这篇博文的所有源代码都可以在 GitHub 上找到。**

**[](https://github.com/Morfly/vulkan-bazel-samples) [## GitHub-Morfly/vulkan-bazel-samples:为应用程序设置环境的示例报告…

### 为使用 Vulkan graphics API 的应用程序设置环境的示例报告，该 API 是用 Bazel - GitHub 构建的…

github.com](https://github.com/Morfly/vulkan-bazel-samples) 

## 参考

*   [**rules _ vulkan**](https://github.com/jadarve/rules_vulkan)**—一个支持 Vulkan 项目的 Bazel 库，由 Juan David Adarve 创建。****