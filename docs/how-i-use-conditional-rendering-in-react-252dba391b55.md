# 我如何在 React 中使用条件渲染

> 原文：<https://medium.com/codex/how-i-use-conditional-rendering-in-react-252dba391b55?source=collection_archive---------2----------------------->

如果使用得当，条件渲染是一个非常有用的 React 概念。您可以根据是否满足某些条件来决定呈现哪些组件，或者完全忽略某个组件。在本文中，我将举例说明如何使用它。

![](img/40e69487f5ee07dfc2da5b1f2238b906.png)

如果您有只想在特定上下文中显示的 UI 元素，请使用条件呈现

```
📕 **THE HANDBOOK USAGE WITH TERNARY OPERATOR**...
  {**someCondition ?** (
    <SomeComponent />
  ) **:** (
    <SomeOtherComponent />
  )}
...
```

相应地，如果`someCondition`为`true`，则 UI 显示`<SomeComponent />`，否则显示`<SomeOtherComponent />`。

当然，您并不局限于使用源自`someCondition`的条件，您可以在`return`之前使用`if`，或者在这种情况下也可以使用逻辑`&&`操作符！

现在让我们来看一些具体的例子:

## 用户管理

当与用户一起工作时，这个概念是必不可少的。在您的应用程序中，一定有这样一个地方，您希望将特定 UI 元素的可见性仅限于登录用户，而不是仅仅浏览的用户。

```
🧍 **CONDITIONAL RENDERING WITH USER MANAGEMENT**return (
  {**props.user ?** (
    <UserDetails name={props.user.name} email={props.user.email} />
  ) **:** (
    <Button>Register for free</Button>
  )}
)
```

## 电子商务

您也可以在电子商务领域使用这些知识。

```
📦 **USE IT IN YOUR E-COMMERCE APP CART**return (
  {**props.cart.items.length > 0 ?** (
    <CartItems items={props.cart.items} />
  ) **:** (
    <Button>See our products</Button>
  )}
)
```

## 组件中缺少数据

使用条件呈现的另一种典型方法是让组件返回一个空 JSX。这种技术有助于防止 UI 显示只有填充和背景的空组件。

```
😢 **EARLY RETURN EMPTY JSX WHEN SPECIFIC FIELDS ARE MISSING****if (!props.title && !props.description) return <></>**return (
  <div className='container'>
    ...
  </div>
)
```

👋我希望这在你寻找新的学习材料时对你有用。感谢您花时间阅读这篇文章！如果你想在未来读到更多这样的故事，请订阅我的电子邮件列表！