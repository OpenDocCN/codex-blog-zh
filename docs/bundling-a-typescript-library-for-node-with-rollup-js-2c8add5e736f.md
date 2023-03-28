# 将节点的类型脚本库与汇总绑定。射流研究…

> 原文：<https://medium.com/codex/bundling-a-typescript-library-for-node-with-rollup-js-2c8add5e736f?source=collection_archive---------0----------------------->

*用 DTS 类型信息和 sourcemaps 封装 NodeJS 库*

当我们在 2021 年开始为 TypeScript 构建 [Lockstep SDK 时，我的目标是打包一个既适用于 TypeScript 项目又适用于 JavaScript 项目的库。我在网上找到了大量的文档，但是大部分信息都已经过时了，导致我找到了一些不推荐的包，或者根本不起作用。](https://www.npmjs.com/package/lockstep-sdk)

为了帮助解决这个问题，我将写下如何为 NodeJS 的 [my SDK](https://tedspence.com/building-sdks-in-five-languages-3add00cc8570) 构建一个有效的类型脚本和 JavaScript 包。我不会记录…