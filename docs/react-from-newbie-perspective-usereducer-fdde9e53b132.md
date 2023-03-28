# 从新手的角度作出反应——用户

> 原文：<https://medium.com/codex/react-from-newbie-perspective-usereducer-fdde9e53b132?source=collection_archive---------4----------------------->

## 当使用状态不够时。

![](img/36152ac8985a8533b64fb819c50ec33f.png)

由 [Ales Nesetril](https://unsplash.com/@alesnesetril?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

附加钩子 useReducer 不是基本钩子之一，它是 useState 钩子的一种替代。但是稍微复杂一点。通过与 Context (Context 和 useContext 将在下一篇文章中介绍)合作，useReducer 可以跨整个应用程序管理组件的状态，在某些特定情况下，它甚至是比 Redux 更好的选择。

当浏览关于 React 的文章时，我经常发现自己有关于处理应用程序状态的观点和过程，其中作者声称 React 已经为处理状态做好了充分的准备，不需要第三方库。状态、缩减器和上下文挂钩的组合在 React base 中。

useReducer 和 useState 一样，存储和更新状态，但两者的工作方式几乎没有什么不同。从我这个新手的角度来看，useState 是针对小的局部变量的，我们需要有受控的状态。另一方面，useReducer 非常适合更复杂的状态，因为它默认情况下处理整个状态对象，例如通过 API 获取数据的状态，我们需要知道我们的应用程序是否正在加载，获取时是否失败或者已经准备好所有数据，useReducer 是一个很好的助手。当然，我们可以对对象或数组使用三个状态变量或一个状态变量，但是使用 reducer 看起来更容易维护。

在接下来的两个代码要点中，我正在创建相同的情况。状态变量*计数*和两个增加和减少其值的函数(*递增*、*递减*)。如果只有一个状态值，那么使用 Reducer 就没什么帮助了，但作为一个例子，它足以说明如何用 useState 和 use Reducer 钩子实现相同的功能。

使用 useState 挂钩

使用 useReducer 挂钩

React Hook useReducer 接受 Reducer 作为第一个参数，初始状态作为第二个参数。Reducer 是一个与 useReducer 挂钩紧密相连的函数，它以数组的形式返回状态和调度。useReducer 返回一个带有当前状态和调度函数的数组。

# 还原剂

Reducer 是普通函数，以之前的状态和动作为自变量，返回新的状态。减速机广泛应用于 Redux 和 React 减速机挂钩。关于减速器，我非常喜欢的一点是使用开关语句，这是一种通过提供的动作来控制减速器输出的方式。

## 状态

状态是具有当前值的对象。可以使用扩展语法完全或部分地更改。

## 行动

Action 是简单的 JavaScript 对象，表示将数据从应用程序发送到存储的有效信息负载。动作有*类型*，这是强制的*有效载荷*，这是可选的。

**型**

类型通常是字符串的类型。我看到了非常有用的方法来防止基于拼写错误的错误，遗憾的是，我不确定是哪篇文章或者是谁写的。使用带有字符串值的变量，因为这样，我们的 IDE 将自动完成并检查所使用的变量是否存在。

**有效载荷**

Payload 是可选参数，它包含带有值的对象，我们使用这些值来更新状态值。

# 结论

React 钩子真的很有帮助，使用 useState 的 useReducer 能够在我们的应用程序中提供我们需要的应用程序状态功能，这取决于我们，我们使用哪种方式。基本上，我们的应用程序越复杂，我们就越有可能需要使用 useReducer，但是在基本的状态变量和应用程序中，我们可以毫不犹豫地使用状态钩子。