# 是什么让输出这么快？

> 原文：<https://medium.com/codex/what-makes-putout-so-fast-54419ce6bc3f?source=collection_archive---------14----------------------->

![](img/1e5ba2fb48abd05fbc26ad13da937183.png)

> “速度不是真正的战略方式的一部分。速度意味着事物看起来快或慢，取决于它们是否有节奏。不管是哪种方式，战略大师都不会很快出现。”—宫本武藏

嗨伙计们！除了 AST 之外，我今天最激动人心的消息是我的速度冒险，所以系好安全带，我们走吧！

# 隐藏物

好的，让我们检查一下🐊[放出](https://github.com/coderaiser/putout)本身。在撰写本文时，它有 **2402** 个文件，包括:
-✅JavaScript；
-✅JSON；
- ✅降价(+ js，ts，JSON)；
——✅YAML；
- ✅ `.gitignore '和`. npmignore '

以下是关于[数字海洋水滴](https://m.do.co/c/331c4947c5df)的结果:

```
coderaiser@cloudcmd:~/putout$ time redrun lint
> putout . — raw — rulesdir rulesreal 0m23.321s
user 0m21.971s
sys 0m1.137s
```

为什么这么快就问？原因是:缓存位于

```
node_modules/.cache/putout/places
```

它是在第一个正确的基础上创建的，有 3 个独特之处:
-node . js 的版本；
-版本🐊输出；
-选项；

当这部分发生变化时，我们将使用`— fresh '标志:

```
coderaiser@cloudcmd:~/putout$ time redrun lint — — fresh
> putout . — raw — rulesdir rules “ — fresh”real 4m24.870s
user 4m16.093s
sys 0m5.757s
```

**4 分钟**，所以大约是 **264 秒，**所以如果我们按时间划分文件数，我们将得到:

```
2402 / 264 = 9
```

**不使用缓存每秒 9 个文件！**

这是跑步的结果🐊**输出** + **ESLint** 这么多支持的格式！而这仅仅是为了一个如此大的项目的新运行。

**记忆化**

> **记忆化——一种优化技术，主要用于通过存储昂贵的函数调用的结果并在相同的输入再次出现时返回缓存的结果来加速计算机程序**。
> 
> 维基网

最简单的插件格式是[**Replacer**](https://github.com/coderaiser/putout/tree/master/packages/engine-runner#replacer)**。**它用了很多🦎 [**输出脚本**](https://github.com/coderaiser/putout/blob/master/docs/putout-script.md#-putoutscript) ，下面是例子:

```
module.exports.report = () => 'any message here';

module.exports.replace = () => ({
    'let __a = __b': 'const __a = __b',
});
```

像这样的表情:

```
let __a = __b
```

被解析成 [AST](https://github.com/coderaiser/putout/blob/master/docs/the-book-of-ast.md#the-book-of-ast) 格式。这是一个扩展的操作，但是这个简短的表达式经常重复，所以记忆版本的解析用来使事情变得更快。

# 什么能让事情变得更快？

在中使用了几个操作🐊**输出**大部分时间:

*   读/写文件；
*   解析文件；
*   从语法上分析🦎**PutoutScript；**
*   穿越；

使用文件系统不会给我们带来任何好处，所以我们可以跳过它。

解析文件更好，但是我们稍后再回来。遍历应该在 JavaScript 端进行，因为所有的插件都是基于 JavaScript 的。

所以我们可以解析🦎**输出脚本**更快**。**如何**？**

## [皮娜-rs](https://github.com/napi-rs/napi-rs)

> 用于在`Rust`中编译`Node.js`附加组件的最小库。

我首先想到的是用 Rust 来加速。但是如前所述，memoization 用于解析目的，当我们需要 [**比较**](https://github.com/coderaiser/putout/tree/master/packages/compare#readme) 节点时，这也是非常快速的操作(大约 2ms)，并且为了使用 Rust 我们需要做一件事情:

*   使用 Rust 遍历 JSON
*   转换为 BSON，然后将结果传递给 Rust，然后再次转换；

这两种情况都很难超越基于 JavaScript 的 2 毫秒**比较**。

## [wasm-bindgen](https://github.com/rustwasm/wasm-bindgen)

> 促进 Wasm 模块和 JavaScript 之间的高级交互。

几乎相同，但基于 WebAssembly 的解决方案。同样，有一个问题没有答案，那就是在铁锈上应该写些什么🤷‍♂️.

## swc

> 基于 Rust 的网络平台

好吧，这是一个解决方案，因为我们仍然有一个文件解析，这肯定是可以加速的。

下面是我们用来比较一个非常大的文件( **44k** )的速度的代码:

```
const {readFileSync} = require('fs');const {parseSync} = require('[@swc/core](http://twitter.com/swc/core)');
const {parse} = require('putout');const source = readFileSync('./lib/cli/index.spec.js', 'utf8');console.time('swc #1');
parseSync(source);
console.timeEnd('swc #1');console.time('swc #2');
parseSync(source);
console.timeEnd('swc #2');console.time('babel #1');
parse(source);
console.timeEnd('babel #1');console.time('babel #2');
parse(source);
console.timeEnd('babel #2');
```

结果如下:

```
swc #1: 13.898ms
swc #2: 13.33ms
babel #1: 236.062ms
babel #2: 132.123ms
```

**SWC** 比**快 10 倍**比**通天塔**！所以我们可以用它来解析。

酪它有不同的内部格式:AST。这个其实是可以解决的，有一个工具[**SWC-to-Babel**](https://github.com/coderaiser/swc-to-babel)**旨在将 **SWC** 格式转换为 **Babel** ，但是差别很大， **SWC** 不支持:**

*   **评论；**
*   **位置数据(已求解)；**
*   **部分解析；**
*   **所有新语法**巴别塔**支持**

**甚至在进行了一些转换之后，像删除节点这样简单的转换也不起作用。使用额外的解析器也会使事情变得不稳定，因为我们已经使用了[重铸](https://github.com/benjamn/recast)、[巴别塔](https://babeljs.io/)和 [ESLint](https://eslint.org/) 。他们每个人都有自己的冒险经历。**

# **结论**

**是的，有些事情是可以改进的，而且肯定会改进！我很感激任何方向的帮助。不管怎样，事情仍然进行得很快。**

**如果你喜欢我正在做的事情，你可以在 [Patreon](https://www.patreon.com/coderaiser) 或 [ko-fi](https://ko-fi.com/coderaiser) 上支持我。如果您需要任何代码转换，只需[创建一个问题](https://github.com/coderaiser/putout/issues/new)，我随时乐意提供帮助🙂。**