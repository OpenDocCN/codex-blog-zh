# UseEffect()挂钩

> 原文：<https://medium.com/codex/the-useeffect-hook-261b0cafaf1e?source=collection_archive---------21----------------------->

![](img/7633de8d848e5ea2c596a3ed9e30e442.png)

由 [Ferenc Almasi](https://unsplash.com/@flowforfrank?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

## 在处理代码中的副作用时，如何实现 React.useEffect()

你好！今天我想把事情稍微改变一下，回顾一下其中一个更令人困惑的 React 钩子。没错，读者们，我说的是`*React.useEffect()*` 及其对 DOM 更新后需要运行的效果的处理方式。我今天在这里帮助你看到这个钩子实际上很容易使用，它的多功能性是没有什么可怕的。让我们直接跳进来吧！

# 反应钩

正如我们所知，React 在 16.8 版本中发布了钩子，让开发人员社区想知道基于类的组件是否会成为过去。基于函数的组件现在有办法声明和更新状态，而不需要原型继承或将事件处理程序绑定到构造函数。显然，事情并没有那样发展，但是使用钩子的基于功能的组件仍然是管理状态的可靠方法。

基于函数的组件可能有一种用`useState`钩子初始化和更新状态的方法，但是如果不使用另一个 React 钩子`useEffect`，它们仍然没有暴露给它们的生命周期方法。`useEffect`钩子只不过为我们的代码提供了一种在 DOM 更新后处理副作用的方法。React.js 文档提供了一个全面的场景，其中`useEffect`可以在 DOM 更新后更新一个值。在示例中，`useEffect`取代了生命周期方法`componentDidMount`和`componentDidUpdate`，这两个方法分别在初始渲染和后续重新渲染时执行。下面的例子展示了一个简单的`useEffect`实现，它将状态集成到 DOM 中，并从 DOM 中取消订阅。

使用 useEffect 更新用户的在线状态(来自 [React Hooks 文档](https://reactjs.org/docs/hooks-effect.html)

React 中的 Effect hook 提供了一个解决方案，用于利用基于函数的组件中的副作用，包括对事件处理程序的控制，该事件处理程序将在初始呈现后侦听依赖项值的变化。组件初始渲染后，朋友状态的任何变化都会触发`useEffect`订阅他们的在线状态。上面的例子包含了第二个参数。使用这个作为第二个参数传递的空依赖数组，以便`useEffect`仅在初始渲染之后执行，而不是在任何重新渲染期间执行。

# 依赖数组

依赖数组用于提供一个监听源，该监听源将在组件最初渲染时和任何重新渲染后触发`useEffect`。如果这个值在以后发生变化，这将导致组件的重新呈现，并且由`useEffect`返回的函数将执行来清除这个副作用(在上面的例子中，它将取消订阅一个朋友的状态变化)。如果没有这个依赖数组，`useEffect`将执行它的默认行为，每次重新渲染时都会发生。缺乏必要的依赖会导致代码中的无限循环，所以让我们避免这种情况。

今天到此为止！查看下面的相关资源列表，包括一些关于 useEffect 及其功能的视频。非常感谢您的阅读！下次见！

# 引用的消息来源

> 拉森，约翰。 *React Hooks in Action:带悬疑并发模式*。第三版。，曼宁出版公司，2020 年，第 98–110 页。
> 
> 网络忍者。"全反应教程# 14——使用效果钩子(基础)." *Youtube* ，Youtube，2021 年 1 月 6 日，[https://www.youtube.com/watch?v=gv9ugDJ1ynU.](https://www.youtube.com/watch?v=gv9ugDJ1ynU.)
> 
> — -."完整反应教程# 15——使用效果依赖." *Youtube* ，Youtube，2021 年 1 月 7 日，[https://www.youtube.com/watch?v=jQc_bTFZ5_I.](https://www.youtube.com/watch?v=jQc_bTFZ5_I.)
> 
> "使用效果挂钩—反应."*React——一个用于构建用户界面的 JavaScript 库*，[https://reactjs.org/docs/hooks-effect.html.](https://reactjs.org/docs/hooks-effect.html.)2021 年 7 月 27 日访问。