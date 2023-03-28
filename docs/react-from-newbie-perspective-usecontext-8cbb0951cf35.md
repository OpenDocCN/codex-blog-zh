# 从新手的角度做出反应——使用上下文

> 原文：<https://medium.com/codex/react-from-newbie-perspective-usecontext-8cbb0951cf35?source=collection_archive---------9----------------------->

> 对我来说，语境是关键——从语境中可以理解一切。
> 
> ***—肯尼斯·达拉***

![](img/5a0de20310afd5f44be24fd60cadf5c8.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Pakata Goh](https://unsplash.com/@pakata?utm_source=medium&utm_medium=referral) 拍摄的照片

# React 中的上下文是什么？

在构建 React 应用程序时，我们经常会遇到这样的情况，当我们需要从父级到子级获取数据时，但有时，它不是直接的子级，而是组件树深处的子级，因此我们可以通过许多组件传递数据，这些组件甚至不需要这些数据(也称为道具钻取)，或者我们需要在如此多的组件中使用这些数据，以至于将数据作为道具传递给所有这些组件是没有意义的。所有这些手动传递数据都可以用不同的方法来完成。当我们的应用程序需要健壮的解决方案时，我们可以使用 Redux，或者我们可以使用内置的 React 上下文。有了上下文，我们就可以共享价值，而无需道具训练或任何其他手动传递。

> 上下文用于共享“全局”数据，如首选语言、用户界面主题或认证用户或他/她的应用程序设置。

但是语境本身并不能自我拯救。当不需要“全局”值时，我们可以使用更简单的方法，比如组件组合，但这不是本文的一部分。

# React 上下文看起来像什么？

## React.createContext 上下文

```
const MyContext = React.createContext(defaultValue);
```

正如方法名所说，它创建了新的上下文对象。上下文对象由两个旨在相互通信属性组成，提供者和消费者。我们甚至可以使用对象析构来创建新的上下文:

```
const { Provider, Consumer } = React.createContext(defaultValue);
```

**供应商**

```
<MyContext.Provider value={/* some value */}>
```

**消费者**

```
function Func() { return (
      <>
         <MyContext.Consumer>
               {value && <div>This is current {value}.</div>}
         </MyContext.Consumer>
      </>
   );}
```

# 那么 useContext 呢？

正如我们所看到，上下文已经完全可用，但是在更复杂的应用程序中，我们可能需要使用不止一个上下文来呈现一个组件，这将导致消费者的嵌套。React Hook useContext 来使代码更干净，并允许我们不使用消费者作为包装来获取所需的值，而是允许我们直接从上下文中获取值。

```
import React, { useContext } from 'react';function Func() {
   const value = useContext(MyContext); return (
      <>
         {value && <div>This is current {value}.</div>}
      </>
   );
}
```

# 结论

上下文本身是非常强大的 React 特性，可以在基本的 React 安装中开箱即用。主要目的是提供需要在我们的应用程序中共享的值和参数，并简化其分发。

React Hook useContext 为我们提供了下一级简化，尤其是在一个组件中使用多个上下文值的情况下。