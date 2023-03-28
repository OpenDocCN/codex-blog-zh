# 设计坚固 React 组件的专业技巧

> 原文：<https://medium.com/codex/pro-tips-for-designing-robust-react-components-c3eaf9cd7ae4?source=collection_archive---------7----------------------->

## “如何让我的 React 应用程序更具响应性和可维护性？”

![](img/d72999e7e7f2d1e606152165c2a6fa26.png)

由[拉斐尔·比斯卡尔迪](https://unsplash.com/@les_photos_de_raph?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/atoms?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

众所周知，React 组件现在是作为函数而不是类来编写的。除此之外，它允许我们免除绑定方法和`this`道具。但是对于这两种方法，您最终都必须编写一个`render`方法，以 JSX 的形式返回 DOM 的一部分。

它们返回 DOM 的一部分，并不生成一个全新的，因为 DOM 更新起来相当昂贵，所以开发人员尽量减少 DOM 更新的次数。

因此，大多数 web 开发人员将组件呈现的数量减少到最小，以减少客户端浏览器和服务器的负载。

React 组件的另一个重要需求是它们能够相当快速地更新它们的 UI。这可以防止用户在应用前端进行不必要的等待，并改善用户体验。

最后，当您的组件可重用时，它会有所帮助。您不仅可以避免两次编写相同的代码，从而满足 DRY(不要重复自己)原则，还可以确信可重用的独立组件的每个实例都将进行最少次数的重新呈现。

在这篇文章中，以及本系列接下来的几篇文章中，我将与您分享一些减少 web 应用程序渲染次数的技巧。

# 尝试对应用程序进行分区，使每个组件都独立于其他组件

原因是，如果您的组件是相互依赖的，那么一个组件中的每个状态更新可能需要另一个组件中的状态更新。这将导致重新渲染，因此当您进行更高级别的组件更新时，您将会多次渲染。理想情况下，您希望在每次高级更新时更新一次组件，但是当然，这并不总是可能的。

如果你试着以一种代表你的应用程序的 UI 布局的方式来划分每个组件，这将会有所帮助。例如，大多数应用程序都有一个带有按钮和链接的标题栏。因此，您应该将按钮组件包含在标题组件中的那个位置。

您创建的每个组件都会增加整个应用程序的复杂性。您必须确保参数是正确的，并且返回的 JSX 是您所期望的，并且，在 arrow 或 lambda 函数的情况下，它们是按照一定的顺序定义的，这样一个函数就不会在文件中调用它上面的另一个这样的 arrow 或 lambda 函数。

尽量让组件的嵌套层次尽可能的扁平。尽管 React 更新 DOM 的方式确保了如果嵌套组件没有在父组件中被修改，它们就不会被重新呈现，但是将组件树变平的好处是它使您可以更容易地单独调试每个组件。

## 何时使用适当析构

方法中的属性析构可以极大地减少属性变量名的长度——如果处理得当的话。首先，不建议同时析构多层属性(嵌套析构),因为您无法验证中间层属性中的数据，这是语义错误的来源。

组件有几十个属性并不少见，所以当你编写功能组件时，仅仅是这些属性本身的拼写就会变得混乱。

当你的道具数量很少时，你应该一级一级的摧毁它们，就像这样:

演职员表:[https://JavaScript . plain English . io/destruct-react-props-with-es6-object-destructing-for-cleaner-code-3984453 e484d](https://javascript.plainenglish.io/destructure-react-props-with-es6-object-destructuring-for-cleaner-code-3984453e484d)

这样可以避免编写像这样连续引用`props`的函数:

演职员表:[https://JavaScript . plain English . io/destruct-react-props-with-es6-object-destructing-for-cleaner-code-3984453 e484d](https://javascript.plainenglish.io/destructure-react-props-with-es6-object-destructuring-for-cleaner-code-3984453e484d)

[](https://javascript.plainenglish.io/destructure-react-props-with-es6-object-destructuring-for-cleaner-code-3984453e484d) [## 用 ES6 对象进行析构来获得更清晰的代码

### 当您在 React 中为组件编写渲染函数时，它会接受一个包含属性的 props 对象…

javascript.plainenglish.io](https://javascript.plainenglish.io/destructure-react-props-with-es6-object-destructuring-for-cleaner-code-3984453e484d) 

以另一个组件为例，我们可以进行两种不同的析构赋值来深入分析属性，相当于嵌套析构:

演职员表:[https://stack overflow . com/questions/60589914/destructing-props-in-react](https://stackoverflow.com/questions/60589914/destructuring-props-in-react)

除此之外，spread 操作符非常适合包含析构变量的赋值的右边。

今天就到这里吧，伙计们。请继续关注下周的帖子，在那里我会写一些管理组件状态的专业技巧。另外，如果你有任何问题，请在下面的评论中告诉我。