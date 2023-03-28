# 用 CSS 变量增强你的样式表

> 原文：<https://medium.com/codex/supercharge-your-stylesheets-with-css-variables-46c2552adccd?source=collection_archive---------10----------------------->

## 让你的 CSS 为你工作，而不是相反。

![](img/3fbd87785a3dc94684dd8e628a0d664d.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

让我们面对现实吧。CSS 很难。保持一切清晰和一致是非常困难的，尤其是当你有一个大项目的时候。你如何简化你的 CSS 工作流程？

# 介绍:CSS 变量。

CSS 变量可以在任何选择器中定义，它们存储十六进制代码、列表、关键字等值。它们在前面用两个破折号声明，然后是一些标识符(即`--<identifier>`)。然后，当我们需要使用它们时，我们可以用`var(--<identifier>)`访问它们。这里有一个例子:

```
/* Declaring a CSS variable: */
--color-white: #e0e0e0;/* Using a CSS variable: */
color: var(--color-white);
```

现在我们知道了如何创建和使用它们，让我们从一些简单的按钮样式开始:

```
.button {
    cursor: pointer;
    color: #e0e0e0;
    background-color: #202020;
}.button:disabled {
    cursor: default;
    color: #808080;
    background-color: #202020;
}.button-action {
    color: #202020;
    background-color: #e0e0e0;
    border: none;
}.button-secondary {
    border: solid 2px #e0e0e0;
}
```

这里有很多多余的代码。属性被重新定义，颜色被重复多次。如果我们不得不改变我们的“白色”会发生什么？我们必须搜索样式表，找到所有以前的“white”实例，并对它们进行更改！

# 全局 CSS 变量对于一般配置来说非常有用。

我们可以从清除这些颜色开始解决这个问题。让我们在`:root`伪元素上定义我们的颜色，这样它们在我们的整个站点上都是可用的:

```
:root {
    --color-white: #e0e0e0;
    --color-grey: #808080;
    --color-black: #202020;
}
```

现在我们已经将它们分开，我们可以将这些颜色插入到我们的按钮样式中:

```
.button {
    cursor: pointer;
    color: var(--color-black);
    background-color: var(--color-white);
}.button:disabled {
    cursor: default;
    color: var(--color-grey);
    background-color: var(--color-white);
}.button-action {
    color: var(--color-white);
    background-color: var(--color-black);
    border: none;
}.button-secondary {
    border: solid 2px var(--color-black);
}
```

搞定了。我们已经有效地将我们的实际风格从调色板中分离出来。然而，现在我们已经做到了这一点，我们可以更进一步。CSS 变量不一定是全局的！

# 局部 CSS 变量对于单个组件来说非常有用。

它们允许我们为某个元素(比如我们的按钮)定义变量，然后根据元素上的其他类有条件地改变它们的值。这意味着我们可以在按钮的基类中拥有所有的样式，只需在其他类中改变变量。

```
.button {
    --border: none;
    --cursor: pointer;
    --foreground: var(--color-black);
    --background: var(--color-white); border: var(--border);
    cursor: var(--cursor);
    color: var(--foreground);
    background: var(--background);
}.button:disabled {
    --cursor: default;
    --foreground: var(--color-grey);
    --background: var(--color-white);
}.button-action {
    --foreground: var(--color-white);
    --background: var(--color-black);
}.button-secondary {
    --border: solid 2px var(--color-black);
}
```

如你所见，CSS 变量让我们有机会让我们的样式表更具表现力和可读性。它还允许我们定义需要全局一致的东西，比如颜色主题和间距。祝你好运，玩得开心！

# 额外资源

*   [MDN:自定义属性](https://developer.mozilla.org/en-US/docs/Web/CSS/--*)
*   [can use:CSS 支持](https://caniuse.com/css-variables)
*   [CSS 技巧:CSS 变量与预处理器变量](https://css-tricks.com/difference-between-types-of-css-variables/)