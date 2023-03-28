# 从新手的角度反应钩子——使用状态钩子

> 原文：<https://medium.com/codex/react-hooks-from-newbie-perspective-281518b239e1?source=collection_archive---------7----------------------->

[React](https://reactjs.org/) 是最常见的 javascript 前端框架之一。事实上，React 是许多其他 web 框架(如 [Next.js](https://nextjs.org/) 或 [Gatsby](https://www.gatsbyjs.com/) )甚至移动开发( [React Native](https://reactnative.dev/) )的基础。作为一个新手，我选择了 javascript，并选择 React 作为我学习的第一个框架。

在我使用 React 的第一步(文档、教程和课程)中，我听说了很多关于一些“钩子”的东西，但不确定它是什么。我不知道类组件和它们的状态。当我开始深入研究 JavaScript 和 React 时，我发现钩子是突破性的新特性，可以帮助开发人员轻松管理 React 特性，如状态、生命周期、上下文和引用。

钩子于 2019 年 2 月 16 日在 16.8.0 版本中呈现。更重要的是，React 团队设法引入了钩子，而不需要修改当前的代码。他们甚至建议除非必要或有计划，否则不要自己重写现有代码。

![](img/c4ae85067cce75384870e281f1968ed7.png)

照片由[沙哈达特·拉赫曼](https://unsplash.com/@hishahadat?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 关于钩子

钩子让我们不用写类就可以使用状态或其他 React 特性。钩子让我们可以编写可重用的代码，在不编写类的情况下为组件使用状态和其他 React 特性。这种独立性提供了共享这种可重用代码的可能性，甚至不是在我们项目的组件之间，而是让我们与其他程序员和社区共享这种钩子。

在 React 16.8 之前，函数组件是无状态的。在 React 中实现状态的唯一方法是使用类。钩子不在 React 类内部工作，但是将状态带入功能组件。

## 功能组件在反应中是什么样子的？)

## React 团队的基本挂钩和自定义挂钩

React 团队给了我们一些有用的现成挂钩。最重要的(正如我现在所说的)是 useState、useEffect、useContext、useCallback 和 useRef。当我们发现它有帮助时(这经常发生)，我们可以创建自己的钩子。例如用于与 API 通信、使用特殊但共享的功能等。或者你可以在 GitHub、npmjs.org 等网站上找到其他程序员定制钩子。例如定时器。真的有必要在每次我们需要使用定时器的时候重写它吗？有了定制的钩子就不是了，你可以在这里找到可爱的[使用定时器钩子](https://github.com/amrlabib/react-timer-hook)由 [Amr Labib](https://github.com/amrlabib) 制作。

![](img/37dec1f63dd801667c6454c95008c72a.png)

照片由[费伦茨·阿尔马西](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

# 使用状态挂钩

useState 是第一个也是最有用的钩子，它作为函数组件来声明状态，就像在类中一样。状态变量在函数调用之间被“保存”,所以我们可以用它们来计数，或者为下一次调用保存我们想要的任何东西。

## 在 React 类中声明状态变量，并使用 React useState 挂钩

## 使用状态参数

useState only 参数是其初始状态。与类不同，初始状态不必是一个对象，可以是“任何东西”。您可以使用 NULL、undefined、String、Int，甚至数组或对象。而且两个都可以空。

## 设置状态变量和数组分解

useState 返回数组中的一对值。在声明新的状态变量时，使用 JavasScript 的“数组析构”语法。数组第一个位置[0]上的值是变量本身，第二个位置[1]上的值是更新变量状态的函数。我们可以随意命名，但标准是[某物，设置某物]。

## 状态变量能容纳什么？

如前所述，状态变量可以保存基本的 JavaScript 变量类型，甚至数组和对象。我们可以将相关的值组合在一起，或者将它们保存在单独的变量中。

## 更新状态变量

我们必须记住，与类中的 this.setState 不同，更新状态变量会替换而不是合并它。当我们想要使用以前的值时，我们需要在设置新值时读取它。

# 结论

作为一个编程界的新手，我仍在学习，并试图通过实践吸收尽可能多的知识。我决定写下我在这个过程中学到的新东西，因为我相信通过学习一个人可以学到东西。React 和它的钩子排在第一位，因为在我看来，它们真的很有趣，很有突破性。在下一篇文章中，我将尝试描述和展示 useEffect 钩子的基础知识。

![](img/8920f6c36df166f941adfb3a57a2eaf1.png)

[斯科特·格雷厄姆](https://unsplash.com/@homajob?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照