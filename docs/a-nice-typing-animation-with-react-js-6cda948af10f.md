# 一个很好的打字动画。Js！

> 原文：<https://medium.com/codex/a-nice-typing-animation-with-react-js-6cda948af10f?source=collection_archive---------3----------------------->

![](img/baa82ed7336943908c4aa075c76b90e9.png)

照片由[伯纳德·赫尔曼](https://unsplash.com/@bernardhermant?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

你尝试过只用普通的 JavaScript 制作打字动画吗？如果你有，你可能已经看到操纵 DOM 有多烦人。这是 React.Js 的好处之一，今天我们将制作一个简单的打字动画组件。假设您已经创建了一个 React。Js 项目之前与'创建-反应-应用程序',也知道一点关于功能组件。

**设置**

首先用 create-react-app 创建你的项目。为此，请打开您的终端并键入

`npx create-react-app typing_anim`

完成所有下载后，通过以下方式将目录切换到项目文件夹

`cd typing_anim`

最后一件事，删除一些样板文件，这样它就不会干扰我们的工作。！我们不会做整个清理工作；转到 App.js，确保它看起来像下面这样。

**创建我们需要的状态**

我们现在准备好开始思考过程了。这里要记住的一点是，我的想法是从数百万个选项中选择的，所以在下面的讨论中分享你的其他解决方案。

我们正在打印一个文本，所以我希望我们的`TypeWriter`组件返回一个`p(paragraph)`标签。但是为了确保在打印文本的同时保留新行等特殊字符，我会使用一个`pre`标签。在这一点上，没有什么有趣的事情发生；如果我们启动我们的应用程序并用浏览器的开发工具检查它，我们只会看到空的`pre`标签，这就引出了我们的下一个问题。应该如何以等于`speed`的间隔不断添加每个字符？

这不是已经暗示了对国家的需求吗？想想吧！我们期望这个组件通过一个`speed`的间隔重新呈现，并显示新的字符。这让我想到两件事。第一个是`setInterval`回调，它将在每个`speed`间隔被调用，并将一个新字符添加到`pre`标签中。第二个是名为`displayedContent`的状态，它最初是空的，当回调被调用时，一个新的字符被添加到其中。由于它是一个状态，每当它发生变化时，react 将呈现组件，我们将获得键入的错觉。

**#简短总结**

我们将有一个`pre`标签来显示一个叫做`displayedContent`的状态。并且使用`setInterval`回调以`speed`的间隔更新该状态。因此，当时间到了，一个新的字符被添加到`displayedContent`组件被重新渲染。

我们需要跟踪的另一件事是哪些字符已经被输入。或者换句话说，哪些字符已经从`content`复制到了`displayedContent`，为此，我们将有另一个状态，称为`index`。我们在这里使用状态，因为这是一个描述组件在其整个生命周期中的行为的变量。

到目前为止，这是我们看到的样子

**是时候考虑设置间隔了**

除非以后需要更多的州，否则我想我们已经准备好了。让我们考虑一下应该如何定义我们的`setInterval`回调。这让我做了一些实验。这是我发现的最好的。由于我们不需要在每次组件呈现时定义多个`setInterval`回调，我们应该找到一种方法只定义一次。一种方法是使用`useEffect`挂钩。如果您以前从未听说过它，它基本上是作为一种只有当依赖关系(状态变量数组)发生变化时才执行函数的方法给出的。而且每当没有依赖的时候，它只会执行我们传递的函数一次(组件第一次挂载的时候)。这是一个简单的演示。

```
useEffect(func, [index])
/*IN this case, after the component have been created if the state index is changed func will be executed but not every time there is a rerender. This is the beauty of the useEffect hook.*/
```

如果你想了解更多关于 react 和所有这些东西的内容，你应该去看看由[罗宾·威鲁奇](https://medium.com/u/a8b7f59b1b73?source=post_page-----6cda948af10f--------------------------------)写的《React 的路线图》。这是我发现的最好的反应书籍之一(不是赞助，真正是我的经验。).如果 YouTube 似乎对你不起作用，是时候你试着从阅读中获取信息了。

**#简短总结**

我们将有一个`useEffect`钩子来启动我们的`setInterval callback`，这个回调将一直更新`index`状态，直到内容用完所有字符。这将每隔`speed`间隔时间进行一次。我们正在记录从`zeroth`到`indexth`索引的子字符串，以查看每次调用 setInterval 回调时是如何添加新字符的。

如果您转到开发人员工具的控制台部分并查看记录的字符串，您可以看到内容正在一步一步地打印出来。如果我们能够在每次更新后通过清空屏幕在 DOM 上反映这一点，我们将有一个漂亮的输入动画。所以，让我们想想如何做到这一点。

我们希望`displayedContent`被更新，这样的事情才会发生，而且它应该以与索引更新相同的速率发生。所以我们可以使用一个新的`useEffect`钩子，它将在索引改变时更新`displayedContent`。

如果我们这样做，我们会得到下面的最终代码。

你现在可以在你的终端上输入`npm start`并欣赏表演。最后一件事，我们可以用一个简单的 CSS 添加闪烁的光标，以获得更好的视觉提示。让我们去我们的`App.css`文件，并在底部添加以下样式。

```
@keyframes blink { from{ opacity: 0%; } to{ opacity: 100%; }}.type-writer::after{ content: "|";animation: blink 1s infinite;}.type-writer > span{}
```

这是 codesandbox.io 的最终演示

**我们将何去何从？**

我们现在已经制作了一个基本的打字动画。我们还可以做更多的事情。我们可以在它周围添加一个很酷的包装器，这样它看起来就像一个真正的代码编辑器。或者我们可以添加颜色支持。我迫不及待地想知道你会用它做什么。在下面的讨论中分享你的发现。如果我写了一个颜色支持，我会在聊天中放一个链接，敬请关注。

任何意见，评论，我都会激动到现在！

编码快乐！！