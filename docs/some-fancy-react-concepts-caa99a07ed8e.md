# 像专业人士一样反应:一些奇特的反应概念

> 原文：<https://medium.com/codex/some-fancy-react-concepts-caa99a07ed8e?source=collection_archive---------5----------------------->

![](img/b728c5e9e573ec993943e19258d0720a.png)

由[萨法尔·萨法罗夫](https://unsplash.com/@codestorm?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果你是 React 新手，我建议你先浏览 React 的[基础知识，然后随着你的进步，你可能想在你的工具箱中添加这些小工具，并在必要时拿出来。👩‍🚒](https://reactjs.org/docs/getting-started.html)

# 1.对救援做出反应:

作为一名软件开发人员，你可能已经使用过一些常见的钩子，比如`useState`、`useEffect`、`useRef`。别担心，我不会让你厌烦的😛

让我们来谈谈那些不经常使用，但如果有效使用，会有很大价值的方法:

**使用命令句柄:**

如果父组件需要直接控制子组件，命令子组件做什么，并希望在其他选项(如回调、上下文等)出现时调用它的方法，那么这个钩子可能是你的防火梯。是不可行的。

React 文档称:

> `*useImperativeHandle*` *定制使用* `*ref*` *时暴露给父组件的实例值。* `*useImperativeHandle*` *应与* `*forwardRef*`配合使用

一般来说，当你使用`useRef`时，你会得到`ref`所附加的组件的实例值，这允许你直接与 DOM 元素交互。`useImperativeHandle`非常相似，但是，它也让你做两件事:

1.  **它允许你决定实例的哪些属性将在引用中公开。不是返回实例元素，而是显式地声明返回值是什么。**

```
const MyComponent = React.forwardRef((props, ref) => { React.useImperativeHandle(ref, () => ({
     functionToExpose: () => {
       console.log("Oh no, I am exposed :/ ");
     }
   }));

   return (
     <div> **Hello there! I am groot**!! </div>
   );
}); const App = () => {
  const ref = React.useRef(null);
  const handleClick = () => {
    console.log(ref.current); //this will contain only one property!
    ref.current.functionToExpose();
  }; return <MyComponent ref={ref} onClick={handleClick} />
}
```

2.**它允许您用自己的函数替换本机函数(如** `**blur**` **，** `**focus**` **等)，**因此使您能够给正常行为添加副作用，或者可能有完全不同的行为。

```
const MyInput = React.forwardRef((props, ref) => {
  const inputRef = React.useRef(); React.useImperativeHandle(ref, () => ({
    blur: () => {
      console.log("I am replacing a native function");
      inputRef.current.blur();
    }
  })); return (
    <input
      ref={inputRef}
      {...props}
    />
  );
});const App = () => {
  const ref = React.useRef(null);
  const onBlur = () => {
    ref.current.blur();
  }; return <MyInput ref={ref} onBlur={onBlur} />;
};
```

**useReducer :**

*在一个中等规模的应用程序中需要处理一堆复杂的状态？useReducer 应该是您的首选。*

**它只接受一个 reducer 函数和一个初始状态，并返回当前应用程序状态和一个调度函数。调度功能通过将动作映射到状态转换来改变状态**:

我曾经不愿意首先使用它，认为`useState`可以很好地工作，直到我意识到它在处理复杂状态时是多么有用，在这种情况下使用 Redux 可能是多余的，但是`useState`可能不是那么有效，因为一旦你的应用程序变大，你很可能会处理更复杂的状态和状态转换。这也有助于提高性能*，因为我们用一个 useReducer 调用替换了多个* `*useState*` *调用(这会导致多次重新渲染)。*

**什么时候用？**

如果您有以下情况，useReducer 是一个不错的选择:

*   JavaScript 对象或数组作为组件的状态
*   复杂的状态转换
*   复杂的业务逻辑，更适合减速器功能
*   应该在一个状态对象中管理的不同属性绑定在一起
*   需要更新组件树深处的状态
*   需要更加可预测和可维护的状态架构

下面是一个简单的例子，展示了`useReducer`的实现

```
function itemsReducer(state, action) {
  *switch* (action.type) {
    *case* "LOADING": {
      *return* { isLoading: *true*, items: *null*, error: *null* };
    }
    *case* "LOADED": {
      *return* { isLoading: *false*, items: action.items, error: *null* };
    }
    *case* "ERROR": {
      *return* { isLoading: *false*, items: *null*, error: action.error };
    }
    *default*: {
      *throw new* Error(`Unhandled action type: ${action.type}`);
    }
  }
}function itemsList() {
  *const* [state, dispatch] = React.useReducer(itemsReducer, {
    items: *null*,
    isLoading: *false*,
    error: *null* });*const* getItems = () => {
    dispatch({ type: "LOADING" });
    fetchItems().then(
      items => {
        dispatch({ type: "LOADED", items});
      },
      error => {
        dispatch({ type: "ERROR", error});
      }
    );
  }
}
```

**useLayoutEffect :**

*如果您的组件在状态更新时闪烁，即它首先呈现在部分就绪状态，然后立即重新呈现在最终状态——这是一个很好的线索，是时候换入* `*useLayoutEffect*`了。

虽然在某种程度上类似于`useEffect()`，但不同之处在于它将在 React 向 DOM 提交更新后运行。

```
useEffect(() => {
   // do side effects
   return () => {} /* cleanup */ 
}, [dependency, array]);useLayoutEffect(() => { 
  // do side effects
  return () => {} /* cleanup */ 
},[dependency, array]);
```

根据 React 文档:

> 签名与`*useEffect*`相同，但它在所有 DOM 突变后同步触发。使用它从 DOM 中读取布局并同步重新渲染。在浏览器有机会绘制之前，`*useLayoutEffect*`中安排的更新将被同步刷新。

**什么时候用** **it:**

因此，如果`useLayoutEffect`会延迟组件的喷漆，我们为什么要使用它。当正在运行的代码以用户可观察到的方式直接修改 DOM 时，应该使用`useLayoutEffect`。

例如，如果我们需要改变 DOM 元素的背景颜色作为副作用，最好使用`useLayoutEffect`,因为我们直接修改 DOM，用户可以观察到这些变化。如果我们使用`useEffect`,我们可能会遇到一个问题，即在运行`useEffect`代码之前绘制 DOM。这将导致 DOM 元素起初是错误的颜色，然后由于`useEffect`代码而变成正确的颜色。

但是，是的，大多数时候你可能甚至不需要它，可以简单地带着`useEffect`离开，直到或者除非你面临它的问题😉

# **2。万能的 HOCs 及其用途**

首先要做的事！什么是 HOC？？🙄
**HOC(高阶分量)是取(包装)一个分量并返回一个新分量的函数。****HOC 的思想是用函数或数据来增强组件。**它们的主要目的是在组件之间共享通用功能，而无需重复代码。抓住你了。

根据你的需要，你可以使用这两个中的任何一个:
a . Props Proxy HOC
b . Inheritance Inversion HOC

**Props Proxy (ppHOC)** 当我们需要操作 Props，抽象状态，或者我们需要用额外的元素包装组件时，这是很有用的。

```
function ppHOC(WrappedComponent) {
  return class myHOC extends Component {
    render() {
      const newProps = {
        title: 'My new title goes here',
        showFeatureX: false,
        showFeatureY: true
      };

      return <WrappedComponent {...this.props} {...newProps} />
    }
  }
}
```

**继承反转(iiHOC)** 另一方面，当我们需要劫持渲染的组件，或者需要操纵状态时使用。

渲染劫持是指我们可以读取、修改*渲染* *、*输出的 React 元素树**，或者有条件地显示元素树，或者读取、添加、编辑、删除*渲染*** 输出的 React 元素**中的任何道具**

|请注意，这里的 render 指的是 WrappedComponent.render

继承反转允许 HOC 通过 *this* 访问 *WrappedComponent* 实例，这意味着**它可以访问状态、道具、组件生命周期挂钩和渲染方法**

在非常基本的层面上，`iiHOC`可以如下实现:

```
function iiHOC(WrappedComponent) {  
  return class myiiHOC extends WrappedComponent {
    render() {
     return super.render()
    }
  }
}
```

# **3。全新的 startTransition API**

让我们欢迎 React 18 版本中引入的这个新 API。`startTransition` **有助于保持当前网页的响应性，同时允许进行大量的非阻塞 UI 更新。**

包装在`startTransition`中的更新被视为非紧急更新，如果出现更紧急的更新，如点击或按键，更新将被中断。假设过渡被用户打断(例如，在一行中输入多个字符)，那么 React 将明智地丢弃没有完成的陈旧渲染工作，只渲染最新的更新。整洁！！

**延迟更新(即从一个 UI 视图到另一个的转换)被称为转换更新。过渡有助于保持大多数交互的整洁，即使它们会导致显著的 UI 变化。另一个优点是:由于您不再呈现陈旧的内容，这也节省了时间。**

`startTransition`的一个很好的例子是当用户开始在搜索框中输入时。虽然输入值必须立即更新，但是搜索结果仍然可以等待几毫秒。

```
// Urgent: Show what was typed
setInputValue(input);

// Wrap your non-urgent updates inside startTransition
React.startTransition(() => {
  // Transition: Show the results
  setSearchQuery(input);
});
```

# 4.严格模式:不会让你犯愚蠢的错误

严格模式就像你的严厉的学校老师，他会因为你愚蠢的错误而不停地戳你😆

这是一个突出应用程序中潜在问题的工具，一种确保你的代码遵循最佳实践的新方法。它是`React.StrictMode`下的一个组件，可以添加到你的应用程序树或子树中。

就像`Fragment`，`StrictMode`不呈现任何 UI，只在运行时激活检查和添加警告。

它有助于:

*   检测具有不安全生命周期的意外副作用和组件
*   验证内部组件是否遵循*一些*建议的做法，如果不遵循，则在控制台中向您发出警告。
*   验证没有使用任何不推荐使用的方法

React docs 说:

> 严格模式是面向开发的，所以您不需要担心它会影响您的产品构建

```
function myComponent() {
  return (
      <React.StrictMode>
        <div>     
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>      
  );
}
```

谢谢你的来访！

我的名字叫 Supreet，我只是一个碰巧喜欢编码的技术爱好者😺

[https://www.linkedin.com/in/supreet-kaur-sandhu/](https://www.linkedin.com/in/supreet-kaur-sandhu/)