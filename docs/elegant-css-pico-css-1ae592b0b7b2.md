# 优雅的 CSS:微微 CSS

> 原文：<https://medium.com/codex/elegant-css-pico-css-1ae592b0b7b2?source=collection_archive---------8----------------------->

![](img/7a347c7fcf04f96a65945d015bc3ce91.png)

Fabrizio Chiagano 在 [Unsplash](https://unsplash.com/s/photos/zen?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

亲爱的读者，当我最后一次发表意见时，我正在开发自己的 css 文件，这些文件符合优雅 CSS 的所有标准:纯粹的“语义”(将内容与外观分离)，没有类，但满足所有基本的 UI 需求。

然后我找到了[Pico CSS](http://picocss.com)——一个我认为连[杰森·奈特](https://deathshadow.medium.com/)都会喜欢的 CSS 文件。以下是我喜欢它的六大理由:

1.  **无阶级。它实际上为每个 HTML5 语义标签设置了样式。在那些你可能真的需要 HTML5 没有提供的东西的地方(例如:滑动开关)，它会把这些东西分配给一个“角色”**
2.  **自定义属性。**几乎每一个我想要改变的参数(宽度、断点、颜色)都是由 CSS3 自定义属性变量设置的，都在文件的顶部。您可以简单地为您自己的项目编辑一个副本，或者您可以链接到 Pico.css CDN 文件并添加一些您自己的 css 行来更改默认参数。
3.  **简洁的设计。我最喜欢的将内容和风格分开的原则是它鼓励好的设计。例如，虽然您仍然可以自由地违反内联样式的良好设计原则，但是 Pico CSS 可以干净地处理开箱即用的所有内容，因此您不太可能这样做。**
4.  **开箱即用。如果不添加额外的 CSS 来处理下拉菜单和拨动开关之类的事情，甚至 Bootstrap 都无法给我一个功能强大、经典、响应迅速的网站。它做到了，没有阶级和无意义的分歧的丛林。**
5.  **没有 JavaScript。**CSS 现在可以做一些我们过去用 JavaScript 完成的奇妙的动态事情，现在也包括在内了。
6.  **重量轻。它并不小——当然我已经创建了更小的 CSS 文件来定义一个站点——但是不到 10 kB 的 minified 和 gzipped 肯定不会降低你的速度。**

很遗憾，我们今天不能用同样的方法重新创建 WordPress 一页的参数设置完全定义了一个网站的设计。