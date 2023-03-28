# 动手反应

> 原文：<https://medium.com/codex/hands-on-react-d768c3ab7919?source=collection_archive---------2----------------------->

2021 新年快乐！！！。这是一个伟大的旅程，从盛行全球疫情。希望你们都平安无事，并希望今年比去年更有成效。在这篇文章中，我想分享我在隔离期间在家时开始学习的关于 React 的知识。

# 什么是反应？

![](img/8933e4fa3854e49902a096a7f2866351.png)

React 是一个 JavaScript 库，可以用来创建用户界面。它创建于脸书，并于 2013 年 3 月开源。如今，React 变得越来越受欢迎，像 Twitter、网飞和微软等公司。

作为一名学生，尝试为一个 web 应用程序构建一个网站或 UI 是非常有趣的，因为在实现过程中它的架构非常简单。React 由组件、元素、道具、状态和生命周期组成。本文将讨论 React 事件和 React JSX 等子部分。

**反应成分**

组件是可重用的代码片段，其工作方式类似于 javascript 函数，因此调用它会返回一些结果。在 React 中有两种类型的组件，如类组件和功能组件。

***类组件*** -该组件被创建为一个从 React 主类扩展而来的类。创建类组件时，组件的名称应该以大写字母开头。

```
class User extends React.Component {
  render() {
    return <h1>Hi, I am a User!</h1>;
  }
}
```

为了在 HTML 代码中使用这个类组件，我们可以使用语法<user>来调用它</user>

```
ReactDOM.render(<User />, document.getElementById('root'));
```

***功能组件-*** 该组件被创建为一个功能，其行为类似于一个类组件。它也可以像调用类组件一样返回到 HTML 代码中。

```
function User() {
  return <h1>Hi, I am also a User!</h1>;
}
```

**道具**

道具就像传递给 React 组件的参数。它还在 HTML 中充当属性。props 可以用在很多方面，而不是像构造函数中使用的传递数据这样的参数。

```
class User extends React.Component {
  render() {
    return <h1>I am a {this.props.name}!</h1>;
  }
}
```

**状态**

状态是存储在组件中声明的属性值的对象。状态对象在构造函数中初始化，并且可以有多个属性。我们可以改变组件中状态的属性。

```
class User extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "Ford",
      Job: "Accountant",

    };
  }
  changeJob = () => {
    this.setState({job: "Teacher"});
  }
  render() {
    return (
      <div>
        <h1>Hi I'm {this.state.name}</h1>
        <p>
          my job is {this.state.job}
        </p>
      </div>
    );
  }
}
```

**生命周期**

生命周期用于在组件完成其动作时释放资源。生命周期中有三个阶段，即*安装、更新和卸载。这些阶段的命名只是为了得到一个基本的了解，* [*点击这里*](https://www.w3schools.com/react/react_lifecycle.asp) *了解更多。*

***挂载***——在 react 中，有四种默认方法可以将组件挂载到 DOM 中。他们是`constructor(), getDerivedStateFromProps(), render(), componentDidMount().`

***更新-*** 在更新状态下，有五种默认方法来更新 DOM 中的组件。他们是`getDerivedStateFromProps(), shouldComponentUpdate() ,render(), getSnapshotBeforeUpdate() componentDidUpdate()`

***卸载-*** 最终状态是当组件从 DOM 中移除时，React 中的默认方法被调用。`componentWillUnmount()`

**元素**

React 元素描述了我们在屏幕上看到的内容。元素通过调用 id、name 等多种方法在浏览器中输出组件输出。

**反应事件**

事件用于 web 应用程序中基于用户的操作，如单击、更改、鼠标悬停等。当写事件时，它们应该用驼色字体写。在某些情况下，可以将参数传递给事件处理程序，而不是创建单独的组件。以下示例描述了向事件传递参数的示例，第二条语句使用*‘bind’*关键字解释了相同的过程。

```
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

**反应过来的 JSX**

JSX 代表 javascript XML，用于在 React 中插入 HTML 代码。XML 在 react 中的主要用途是，不用 createElement()或 appendChild()，很容易在 Javascript 中添加 HTML 元素并放入 DOM 中。

我想这总结了我的文章，它包含了 React 中的主要概念，但还有许多其他重要和高级的概念，如列表和键、表单和渲染、钩子等。此外，React 有许多版本，如 NEXT.js、Gatsby、React Native、Preact 和 React 360，用于开发多宇宙 web 应用程序多个目的。作为一个绝对的初学者，学习 react 的最佳资源是我曾经学习过的 React 的 React.org 文档页面和 T21 Mozilla 页面。有许多像 W3Schools 和 LinkedIn learning 这样的解决方案，可以根据你自己的水平来学习它们，我鼓励去学习这个让我着迷的神奇工具。干杯！！！