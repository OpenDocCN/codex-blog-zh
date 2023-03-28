# 风格化 CSS:用类似 CSS 的工具更快地编写你的 Laravel 网站

> 原文：<https://medium.com/codex/stylify-css-code-your-laravel-website-faster-with-css-like-utilities-3fc065c5a976?source=collection_archive---------17----------------------->

![](img/6e9d5f7150865088f9d9e9c1eeb445f8.png)

用类似 CSS 的工具更快地编写你的 Laravel 网站。不要研究 CSS 框架。专注于编码。

# 介绍

[Stylify](https://stylifycss.com/) 是一个库，它使用类似 CSS 的选择器根据你写的内容生成优化的实用优先的 CSS。

*   ✨类 CSS 选择器
*   💎没有研究的框架
*   💡花在文档上的时间更少
*   🧰错位&极小的 CSS
*   🤘不需要清洗
*   🚀组件、变量、自定义选择器
*   📦它可以生成多个 CSS 包

此外，我们有一个页面是关于[样式化 CSS 解决了什么问题以及为什么你应该试一试！](https://stylifycss.com/docs/get-started/why-stylify-css)

# 装置

本文使用 Laravel 和 Vite。对于带有 Webpack 的旧版本，请查看本指南。

使用 CLI 安装 Stylify:

```
npm i -D @stylify/unplugin
yarn add -D @stylify/unplugin
```

将以下配置添加到`vite.config.js`:

```
import { defineConfig } from 'vite';
import { stylifyVite } from '@stylify/unplugin';

const stylifyPlugin = stylifyVite({
    // You can define multiple bundles, This example uses only one for simplicity
    bundles: [{ files: ['resources/views/**/*.blade.php'], outputFile: 'resources/css/stylify.css' }],
    // Optional - https://stylifycss.com/docs/unplugin
    compiler: {
        // https://stylifycss.com/docs/stylify/compiler#variables
        variables: {},
        // https://stylifycss.com/docs/stylify/compiler#macros
        macros: {},
        // https://stylifycss.com/docs/stylify/compiler#components
        components: {},
        // ...
    }
});

export default defineConfig(() => ({
    plugins: [
        stylifyPlugin,
        laravel({
            // Add path to generated files
            input: ['resources/js/app.js', 'resources/css/stylify.css'],
            refresh: true,
        }),
    ],
}));
```

将`resources/css/stylify.css`的路径添加到模板中:

```
<head>
    @vite('resources/css/stylify.css')
</head>
```

如果你在`stylifyVite`插件中定义了多个包，确保在正确的页面上导入生成的 CSS 文件。

# 使用

Stylify 语法类似于 CSS。你只需写`_`而不是空格，写`^`而不是引用。

所以如果我们编辑`resources/views/welcome.blade.php`:

```
<div class="text-align:center font-size:48px color:blue">Stylify + Laravel = 🚀</div>
```

在生产中，你会得到优化的 CSS 和混乱的 HTML:

```
<div class="a b c">Stylify + Laravel = 🚀</div>
```

```
.a{text-align:center}
.b{font-size:48px}
.c{color:blue}
```

# 集成示例

您也可以在 Github 上查看我们的 [Laravel 集成示例](https://github.com/stylify/integrations-examples/tree/master/laravel)。

# 配置

上面的例子没有包括 Stylify 能做的所有事情:

*   定义[组件](https://stylifycss.com/docs/stylify/compiler#components)
*   添加[自定义选择器](https://stylifycss.com/docs/stylify/compiler#customselectors)
*   像配置`ml:20px`一样配置[宏](https://stylifycss.com/docs/stylify/compiler#macros)的左边距
*   定义[自定义屏幕](https://stylifycss.com/docs/stylify/compiler#screens)
*   您可以在模板中映射[嵌套文件](https://stylifycss.com/docs/bundler#files-content-option)
*   还有更多

请随意[查看文档](https://stylifycss.com/docs/get-started)了解更多信息💎。

# 让我知道你的想法！

如果你喜欢这个想法，让我知道由❤️.主演的

我也将为任何反馈感到高兴！[风格化](https://stylifycss.com/)仍然是一个新的图书馆，有很大的改进空间🙂。