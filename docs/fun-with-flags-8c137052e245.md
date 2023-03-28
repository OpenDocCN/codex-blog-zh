# 旗帜带来的乐趣

> 原文：<https://medium.com/codex/fun-with-flags-8c137052e245?source=collection_archive---------5----------------------->

![](img/1d7bdccfc3d3f98e7d86bae1025748bd.png)

由[弗拉季斯拉夫·克拉平](https://unsplash.com/@lemonvlad?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

bazel build system 最初是 Google 内部的一个构建工具，是一个开源项目，专注于跨各种编程语言和平台的快速、可靠和可重复的软件开发。

一个典型的 bazel 项目是一个目录树，在树的根部有一个名为`WORKSPACE`的文件，它定义了外部依赖关系，还有一个或多个名为`BUILD`(或`BUILD.bazel`)的文件，其中包含构建目标的声明。更多信息和例子可以在[这里](https://bazel.build/start)找到。

最近，在一个项目中，我需要一种方法来根据用户选择的值在几个不同的版本之间进行选择。Bazel 使用[用户定义的构建设置](https://bazel.build/rules/config#user-defined-build-settings)来支持这一点。为了简单起见，我们将使用`[bazel_skylib](https://github.com/bazelbuild/bazel-skylib)`库，它包含一些定义构建设置的规则。我们需要在我们的`WORKSPACE`文件中声明这一点:

```
load("[@bazel_tools](http://twitter.com/bazel_tools)//tools/build_defs/repo:http.bzl", "http_archive")http_archive(
    name = "bazel_skylib",
    urls = [
        "[https://mirror.bazel.build/github.com/bazelbuild/bazel-skylib/releases/download/1.2.0/bazel-skylib-1.2.0.tar.gz](https://mirror.bazel.build/github.com/bazelbuild/bazel-skylib/releases/download/1.2.0/bazel-skylib-1.2.0.tar.gz)",
        "[https://github.com/bazelbuild/bazel-skylib/releases/download/1.2.0/bazel-skylib-1.2.0.tar.gz](https://github.com/bazelbuild/bazel-skylib/releases/download/1.2.0/bazel-skylib-1.2.0.tar.gz)",
    ],
)
```

这使用了内置`bazel_tools`库中的`http_archive`规则，我们用它来导入`bazel_skylib`存储库。

现在我们可以在`BUILD`文件中为我们的项目添加一个构建设置:

```
load("[@bazel_skylib](http://twitter.com/bazel_skylib)//rules:common_settings.bzl", "string_flag")

string_flag(
    name = "foo",
    build_setting_default = "42",
)
```

在我的项目中，我需要一种方法在一些 C++代码中使用这个构建设置的值。这就是事情开始变得有点棘手的地方。首先，让我们添加程序代码:

```
// test.cpp
#include <iostream>
using namespace std;
int main() {
    cout << FLAG << endl;
    return 0;
}
```

按照现在的情况，除非我们在 C 编译器命令行中添加一些东西来为`FLAG`定义一个值，否则它不会编译。我们可以在我们的`BUILD`文件中使用`cc_binary`规则的`defines`属性来做到这一点:

```
cc_binary(
    name = "test",
    srcs = ["test.cpp"],
    defines = ["FLAG=99"],
)
```

现在我们需要弄清楚如何将我们的构建设置链接到代码中的`FLAG`值。为此，我们将使用 bazel Starlark 语言定义一个定制规则。我们添加了一个新文件`defs.bzl`，它包含规则实现:

```
# defs.bzl
load("[@bazel_skylib](http://twitter.com/bazel_skylib)//rules:common_settings.bzl", "BuildSettingInfo")def _define_from_flag_impl(ctx):
    return CcInfo(
        compilation_context = cc_common.create_compilation_context(
            defines = depset([
                "FLAG=\"{}\"".format(
                    ctx.attr.value[BuildSettingInfo].value,
                ),
            ]),
        ),
    )define_from_flag = rule(
    implementation = _define_from_flag_impl,
    attrs = {
        "value": attr.label(),
    },
)
```

分解一下，我们定义了一个名为`define_from_flag`的新规则，它有一个名为`value`的带标签的属性。该属性将与引用我们的构建设置的标签一起使用。如需进一步阅读，请查看更深入讨论该主题的[规则教程](https://bazel.build/rules/rules-tutorial)。

在 Starlark 中，标签包含一个[提供者](https://bazel.build/rules/lib/starlark-provider)的集合，这些提供者用标签描述对象的细节。对于`bazel_skylib`的`string_flag`规则，这包括一个保存标志值的`BuildSettingInfo`——我们将它从标签中取出，并用它来格式化`FLAG`的定义设置。最后一部分将该定义包装在一个`CcInfo`提供程序中，该提供程序的行为类似于一个包含我们的定义值的空`cc_library`。

现在我们可以在我们的`BUILD`文件中把它连接起来:

```
load("[@bazel_skylib](http://twitter.com/bazel_skylib)//rules:common_settings.bzl", "string_flag")
load("defs.bzl", "define_from_flag")cc_binary(
    name = "test",
    srcs = ["test.cpp"],
    deps = [":flag"],
)define_from_flag(
    name = "flag",
    value = ":foo",
)string_flag(
    name = "foo",
    build_setting_default = "42",
)
```

这添加了一个`define_from_flag`的实例作为我们`cc_binary`的依赖项。因为定义从库传播到目标依赖于那些库，这将自动添加一个`-DFLAG=”42"`到编译器命令行。

最后，我们需要弄清楚如何更改构建设置值。幸运的是，这很容易—可以使用 bazel 命令行来更改标志值:

```
$ bazel run:test --//foo=hello
...
hello
```

我们可以通过在`.bazelrc`中定义一个别名来使其更加用户友好:

```
# .bazelrc
build --flag_alias=foo=//:foo
```

这个例子的所有代码都在 github [这里](https://github.com/dfr/fun_with_flags.git)。