# 如何在自定义链接中使用 React Bootstrap Navbar 的 collapseOnSelect 属性

> 原文：<https://medium.com/codex/how-to-use-react-bootstrap-navbars-collapseonselect-property-with-custom-links-b2902ff5f472?source=collection_archive---------5----------------------->

![](img/72b1c85e254b44f3bf489d56649e2c3e.png)

由[明陈](https://unsplash.com/@minhctran?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

React Bootstrap 提供了一个方便、简单且响应迅速的设计系统，使用可重复使用的组件为您的网站添加功能、风格和结构。有几个方面的事情可能会变得有点复杂，比如让 Navbar 及其 CollapseOnSelect 属性按预期工作。环顾互联网，似乎我不是唯一一个遇到困难的人。好消息是，我最近将 [Cork Hounds](https://blog.corkhounds.com/p/cb7c1e64-54c8-4a89-b281-a99be1b332c4/www.corkhounds.com) 从 React Bootstrap v0.33.1 更新到了 v1.6.4，我终于能够让 CollapseOnSelect 与 React 路由器链接和哈希链接一起工作了。解决方案相对简单，但是这是一个有一个工作示例真的很有帮助的例子。因此，我将从重点开始，最后概述 codesandbox.io 中的一个全功能解决方案。

# 概观

那么，什么是 CollapseOnSelect，为什么我们需要一个专门针对它的博客？CollapseOnSelect 是 Navbar 组件的一个(挑剔的)设置，主要在两种情况下发挥作用:

*   当您使用下拉菜单时，您希望在用户单击 NavDropdown 后关闭它。项目，或
*   当你使用导航条时。当你的应用程序显示在小屏幕上时，折叠组件会自动将你的导航条转换成汉堡菜单。当用户做出选择时，您希望菜单折叠。注意，`expand` prop 允许在不同的断点折叠导航条。

使用 Navbar 组件时，您通常需要适应(1)内部路由，(2)锚定标签/引用，或(3)外部站点。由于大多数有内部路由的 React 站点使用 React 路由器，我们将使用[链接](https://v5.reactrouter.com/web/api/Link)。对于锚点引用，React 路由器链接提供了 [innerRef](https://v5.reactrouter.com/web/api/Link/innerref-refobject) 属性。您的 Navbar 组件需要能够访问 refs，有很多方法可以做到这一点。你也可以使用锚标签(或者散列链接)，例如`id="first"`。React 路由器不支持锚标签，但是有一个名为 [React 路由器散列链接](https://www.npmjs.com/package/react-router-hash-link)的库提供了该功能。为了与 ReactJS 的最佳实践保持一致，说直接引用 DOM 中的元素是一种反模式可能很重要，并且您应该[使用 refs 而不是](https://reactjs.org/docs/refs-and-the-dom.html)。但是有些情况下不方便使用 refs，你只需要让一些东西工作起来就可以了。链接到外部 URL 的第三个也是最后一个用例是使用导航。链接`href`道具，除非需要特殊处理。

使用 CollapseOnSelect 属性的最终目的是当用户点击这些链接时，按预期折叠菜单。此外，您希望将用户发送到他们期望的精确位置(例如，在锚定标签/引用的顶部，或者在适当的内部页面上)。当我们考虑到 Navbar 这个事实时，这里就有点复杂了。当用户与菜单交互时，Collapse 改变标题的高度。我们想要解决这个问题，这样当导航到一个锚标签/引用时，它就不会干扰/偏移页面。

因为使用 React 路由器散列链接是“自定义”处理的象征，所以我将通过一个示例演示如何使链接和散列链接与 Navbar 和 CollapseOnSelect 属性一起工作。

# 假设和开始

对于这篇博客，我假设您已经相当好地掌握了 JavaScript、包管理(npm 或 yarn)和 ReactJS，包括 React Router (react-router-dom)，并且熟悉 React Bootstrap。我试图通过在 codesandbox.io 中提供一个由 create-react-app 生成的完全可用的示例[来缓解这些障碍，您可以使用 React Bootstrap Navbar 来启动您的项目。我们的项目使用以下依赖关系:](https://codesandbox.io/s/react-bootstrap-collapseonclose-mqewn)

```
{"bootstrap": "4.6.1", "react": "16.14.0", "react-bootstrap": "1.6.4", "react-dom": "17.0.2", "react-router-dom": "5.3.0", "react-router-hash-link": "2.4.3", "react-scripts": "4.0.0"}
```

# 密码

让我们先来看一个使用 CollapseOnSelect 和 Nav 的导航条的精简示例。链接和导航下拉菜单。项目，带有用于页面的标准 React 路由器链接和用于锚标签的自定义链接实现(也称为哈希链接)。

在这个例子中有一些事情需要注意。首先，我们显示两个导航。链接和两个 NavDropdown。物品；每一个都说明了如何使用一个标准的 React 路由器链接和一个自定义链接，我们稍后将对此进行详细说明。React 路由器链路将开箱即用；不需要特殊处理/考虑。然而，对于一个定制的散列链接，我们需要将标题高度传递给`MyHashLink`组件，以便它知道滚动位置偏移多远。

要做到这一点，让我们看看一个基于类的方法来加载 Navbar，精确计算标题的高度并将值存储在组件状态中，我们称之为`this.state.headerHeight`。我们将使用两个步骤来完成此操作。首先，当组件挂载时，我们通过从 `componentDidMount()`生命周期方法中调用`updateHeaderHeight()`方法来设置 headerHeight。注意，在`updateHeaderHeight()`方法中，我们使用`this.divElement.clientHeight`来获取我们在 div 容器上设置的`ref`的高度，在上面提供的例子中，div 容器包装了 Navbar。

第二，因为图像加载很慢，所以我们希望在头部图像加载时更新 headerHeight。您会注意到，在下面的代码片段中(为了方便起见，从上面的 Navbar 部分转述而来)，我们调用了`this.updateHeaderHeight()`来完成这个任务。

现在我们有了一个准确的标题大小，我们可以看看我们的定制`MyHashLink`组件。下面所示的功能组件接收与 Nav 相关联的属性。链接和导航下拉列表。项，包括`data-height`、`to`等重要属性。

在我们的自定义示例中，我们希望滚动到一个锚标记。我们将使用 [React 路由器哈希链接](https://www.npmjs.com/package/react-router-hash-link)库来完成这项工作。这个库导出两个组件:`HashLink`和`NavHashLink`。我们将使用`NavHashLink`,因为当链接被点击时，它正确地将活动的`className`应用到框外。为了在点击`NavHashLink`时触发`Navbar`折叠，我们设置了它的 onClick 属性:`onClick={props.onClick}`。

接下来，我们使用 scroll 属性将元素传递给我们的`scrollWithOffset`函数来定制滚动行为。这里，当滚动到选定的锚标记时，我们使用传入的`props["data-height"]`作为 yOffset。

这就是我们需要为 collapseOnSelect 做的所有特殊处理，以便与 React 路由器链接和自定义哈希链接实现一起正常工作。

# 工作示例

让我们把这些放在一起。没有什么比有一个完全可行的例子来说明一切是如何结合在一起的更好了。这个项目是在 codesandbox.io 中使用 create-react-app (CRA)创建的。这个应用程序的入口点是`src/index.js`文件，我们在其中引入了上面描述的 Header 类，并设置了 React Router。

基本路由“/”指向`App`组件，还有一些其他示例路由指向`PageComponent`。在`App`组件中，我们引入了`HashComponent`来说明在整个页面中使用多个锚标签。`Header`和`MyHashLink`组件协同工作以提供导航。我们将 React 路由器链接映射到`index.js`中指定的路由，这些路由指向一个`PageComponent`，而我们的锚标记指向一个`HashComponent`。

当点击菜单项时，菜单将按预期折叠，并导航到适当的锚或页面。请随意探索这一点，并在您认为合适的时候加以利用。

# 结论

希望这篇博客对你有所帮助。如果您觉得这很有用或者需要任何帮助，请留下您的评论！

*原载于 2021 年 11 月 20 日*[*【https://blog.corkhounds.com】*](https://blog.corkhounds.com/p/cb7c1e64-54c8-4a89-b281-a99be1b332c4/)*。*