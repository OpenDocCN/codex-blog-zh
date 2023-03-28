# 理解钩子第 7 部分—状态管理

> 原文：<https://medium.com/codex/understanding-hooks-part-7-state-management-84ff636834a7?source=collection_archive---------11----------------------->

## 传说中的 React 钩子背后是什么？有时候，我会想。

![](img/8e42cee1f7d92219ceebab9b4725eb57.png)

在第 6 部分中，我们研究了一个相对复杂的钩子`useContext`,看看如何使用状态来驱动元素更新。在第 7 部分中，我们将基于这种理解创建我们自己的状态管理。

我们知道现在调用`setState`触发了`useState`注册的`Component`的更新。

先说一个`value`和`setValue`。

## 单一的

最常见的情况是在事件处理程序中使用`setValue`，比如 click。

```
const Single = () => {
  const [value, setValue] = **useState**(1)
  const onClick = () => { setValue(2) } 
}
```

或者您可以在`useEffect`中使用它，当您获取一个 API 并成功设置值时。

```
const Single = () => {
  const [value, setValue] = **useState**(1)
  useEffect(() => { setValue(3) } 
}
```

## 父母/子女

您可以将`setValue`传递给一个子组件。这允许孩子向父母请求更新。

```
const Parent = () => {
  const [value, setValue] = **useState**(1)
  return <Child setValue={setValue} /> 
}
```

更常见的方法是让孩子暴露一个事件，

```
const Parent = () => {
  const [value, setValue] = **useState**(1)
  const onChange = () => { setValue(3) }
  return <Child onChange={onChange} />
}
```

`Child`通过`onChange`通知父节点，父节点反过来设置值，从而请求更新父节点(和子节点)。

## 祖先/子女

如果一些孩子以某种方式连接，可以创建一个`Context`并通过`useContext`从祖先传递给特定的孩子。

```
// ancestor 
const Context = createContext()
const Ancestor = () => {
  return <Context.Provider value={**useState**(1)} />
}
// child
const Child = () => {
  const [value, setValue] = useContext(Context)
}
// not connected child
const Child2 = () => {
  // does not have useContext 
  // or with another Context2
}
```

对于连接到同一个`Context`的所有子节点，调用`setValue`请求立即更新祖先和所有连接的子节点。但是，它可以跳过一个孩子，不在这个网络中。

> 组件是否渲染取决于它自己的属性和状态。当祖先更新时，不在网络中的孩子仍然可以得到更新，例如，孩子可以接收改变的属性。

## 网络

以上案例传`setValue`左右。因为`useState`是在一个主机元素中定义的，按照设计，当`setValue`被调用时，它请求更新主机。无论你把它发到哪里，它仍然适用于主机。这使得去中心化变得困难。

让我们退一步。与其四处传递，不如为网络中的任何组件动态创建`useState`。

```
const **useCustomState** = createUseState(1)
const Component = () => {
  const [value, setValue] = **useCustomState**()
}
```

现在，如果我们调用`setValue`，它会请求更新`Component`。

```
export default function **createUseState**(initialValue) {
  const listeners = []
  const value = initialValue
  const setValue = v => {        
    current = Object.assign({}, current, v)
    listeners.forEach(listener => listener())
  } return function **useCustomState**() {
    const [self, set] = useState(value)
    useEffect(() => {
      const update = () => {
        if (self !== value) set(value)
        listeners.push(update)
        return () => {
          listeners = listeners.filter(i => i !== update)
        }
      })
    }
    return [self, setValue]
  }
}
```

这张图片中没有主机元素，它是一个为`value`提供持久存储的网络。每次调用`useCustomState`都会创建一个新的`useState`。并且调用`setValue`更新这个`value`并触发网络内所有连接的元件。

## 状态管理

现在我们可以在这个项目中管理一个状态的生命周期。我们可以自由地读/写它，并准确地使用它来更新显示。更何况我们可以从一个状态管理系统，比如`redux`来问。

让我们看看能否将上述自定义状态投入到更实际的使用中`useUser`。

```
const useUserState = createUseState()const **useUser** = () => {
  const [user, setUser] = useUserState()
  useEffect(() => { // login or fetch api
    setUser(...)
  }, [])
  return user
}function Avatar () {
  const user = **useUser**()
  if (!user) return 'loading...' return <img src={user.avatar} />
}
```

尽管它是由`useState`和`useEffect`组成的，但是`useUser`看起来更像是一个数据层，就像传统的方式一样。最后，我们希望将`user`对象建模为

*   与唯一用户同步
*   当用户改变时通知所有连接的元素

如果这一切都完美运行，最后一个问题是，这个规模？例如，如果我们有 10–20 个`useUser`的使用，更新会导致大量的渲染甚至是一个死循环吗？幸运的是，`react`团队解决了这个问题。

> *"理解这一点的关键是，* ***无论你在一个 React 事件处理程序*中做多少**** `***setState()***` ***调用多少个*组件，它们都只会在事件** *结束时产生一个单独的重新渲染。这对于大型应用程序中的良好性能至关重要，因为如果在处理点击事件时* `*Child*` *和* `*Parent*` *都调用了* `*setState()*` *，那么您就不希望两次重新渲染* `*Child*` *。* [*丹在这张票上评论了*](https://stackoverflow.com/questions/48563650/does-react-keep-the-order-for-state-updates) *，这里演示了*[](/swlh/react-state-batch-update-b1b61bd28cd2)**。**

## *TL；速度三角形定位法(dead reckoning)*

*状态管理可以用一个`useState`来完成。可以在多个位置设置和使用状态。并且在改变时，它通知所有连接的元件。*

# *索引*

*![](img/d48d45c7dcf9d31d710783b3ed483803.png)*

*由[约书亚·阿拉贡](https://unsplash.com/@goshua13?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄*

*   *[第 1 部分，元素](https://windmaomao.medium.com/hook-myth-from-react-9495aa8ad7af)*
*   *[第二部分，使用效果](https://windmaomao.medium.com/hooks-part-2-useeffect-2fa1a377c124)*
*   *[第 3 部分，使用状态](https://windmaomao.medium.com/hooks-part-3-usestate-26a622bbe462)*
*   *[第 4 部分，挂钩](https://windmaomao.medium.com/understanding-hooks-part-4-hook-c7a8c7185f4e)*
*   *[第 5 部分，自定义挂钩](https://windmaomao.medium.com/understanding-hooks-part-5-custom-hook-985b83c8bfea)*
*   *[第 6 部分，使用上下文](https://windmaomao.medium.com/understanding-hooks-part-6-usecontext-7ece0c0818e3)*
*   *第七部分，状态管理你在这里*

**使用的代码片段和思想大量借鉴并简化自*[use SWR](https://swr.vercel.app/)[反冲](https://github.com/facebookexperimental/Recoil)。*