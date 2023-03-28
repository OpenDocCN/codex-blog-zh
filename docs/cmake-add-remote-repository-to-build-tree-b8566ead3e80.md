# CMake:添加远程存储库以构建树

> 原文：<https://medium.com/codex/cmake-add-remote-repository-to-build-tree-b8566ead3e80?source=collection_archive---------4----------------------->

![](img/46a44bb9264c4e3c4b44b2031814b60f.png)

cmake logo 来源:cmake.org

在使用 CMake 构建项目时，当我的项目依赖外部库组件时，我经常会遇到问题。例如，在开发的早期阶段最经常需要的测试框架必须在存储库中下载/克隆，然后在测试框架成为构建依赖树中的节点之前，必须在 CMakeLists.txt 中手动添加测试框架的路径。

![](img/153414607b6655e7303cb6d1edf6e094.png)

其他选项可能需要使用依赖管理器，如[柯南](https://conan.io/)或项目中的手工脚本来完成这项工作。不管用什么方法，解决这样的依赖问题都不是 CMake 的 DNA。但这在 CMake 3.11 中有所改变，增加了 FetchContent 宏。
让我们看看他们的行动。

首先，让我们看看 CMakeLists.txt 的基本框架，其中创建了一个测试可执行文件并链接到 gtest 库:

```
## CMakeLists.txt ##
cmake_minimum_required(VERSION 3.11)
project(FetchContentDemo LANGUAGES CXX)

enable_testing()

###############################
# FETCH GTEST FROM REPOSITORY #
###############################add_executable(fetchGtestEg testMain.cpp)
target_link_libraries(fetchGtestEg PRIVATE gtest)
add_test(NAME fetchGtestEg COMMAND fetchGtestEg)
```

上面的 CMakeLists.txt 文件有序言，从远程获取 [gtest](https://github.com/google/googletest) 库之后，测试可执行文件可以简单地链接到测试库。让我们看看如何获取 gtest 存储库并将其添加到构建树中。下面的代码片段表示应该替换注释块的部分

```
include(FetchContent)    #Note thisFetchContent_Declare
(gtest                   #name of repository
 GIT_REPOSITORY https://github.com/google/googletest.git
 GIT_TAG        release-1.11.0
)FetchContent_Populate(gtest)
```

通过 CMakeList.txt 中的这几行代码，开发人员可以指定测试框架的版本，并轻松地分发 cmake 文件，而无需担心测试框架的相关版本。希望你喜欢阅读这篇文章。