# 如何在 TypeScript 中键入 React props

> 原文：<https://medium.com/codex/how-to-type-react-props-in-typescript-f40c41ff3c6f?source=collection_archive---------0----------------------->

道具在 React 中是必不可少的。它们可以在中使用，并且您可以为组件提供各种值和功能。因为不同的道具可以在组件中用作对象键，所以可以对它们进行析构和类型化。

![](img/db8d5b359fea90172ac0b00d95afd405.png)

向组件添加道具非常简单

```
⚛️ **ADDING PROPS:** <BlogPostComponent 
  title='First post'
  subtitle='Created by me'
/>
```

这是你如何在室内使用它

```
🤸‍♂️ **USING THEM IN THE COMPONENT:**
const BlogPostComponent = (props) => {
  return(
    <div>
      <p>{**props.title**}</p>
      <p>{**props.subtitle**}</p>
    </div>
  )
}
```

现在让我们更仔细地检查函数组件的参数中的单词`props`。正如您所看到和回忆的，它基本上等于一个对象，您可以像引用任何其他对象一样引用它的值(`props.title`和`props.subtitle`),因此如果您使用 TypeScript，您也可以键入它们。

通常，程序员们只使用 TypeScript，因为它是如下流行的类型道具:

```
❌ **PLEASE DON'T DO THAT**
const BlogPostComponent = (**props: any**) => ...
```

我们可以做得更好。详细定义对象怎么样？我们已经知道我们想要传递的属性，所以`props`将是一个带有`title: string`和`subtitle: string`字段的对象:

```
✅ **THIS COULD WORK** 
const BlogPostComponent = (props:
  { **title: string, subtitle: string** }
) => ...
```

这真的很棒。您非常清楚`props`和您必须提供给组件的类型。现在让我们探索一些额外的东西:

## 可选道具

在使用 React 和简单 JavaScript 时，几乎所有的`props`都是可选的。如果你错过了通过一个，比如说，只通过了`title`的`BlogPostComponent`，就不会出问题。

对于 TypeScript 和类型化属性，情况完全不同。一旦它们被输入到组件中，我们必须清楚地说明哪些是必需的，哪些是可选的。幸运的是，我们有可选属性的`?`符号，允许我们在组件中使用它。

```
🚩 **THE SUBTITLE IS OPTIONAL**
const BlogPostComponent = (props:
  { title: string, **subtitle?: string** }
) => ...
```

下面是没有它的组件:

```
👍 **YOU CAN DEFINE YOUR COMPONENT WITHOUT IT**
<BlogPostComponent 
  title='Second post without a subtitle'
/>
```

## 解构

```
💡 *Allows values from arrays or properties from objects to be unpacked into separate variables.*
```

在上面的每个例子中，我们都提到了我们应该提到的`props`对象键:`props.title`和`props.subtitle`。这意味着我们想要处理`props`对象的`title`和`subtitle`字段。使用这些字段的一种更优雅的方式是在组件的参数中析构它们，下面是如何做的:

```
💃 **PROPS FIELDS CAN BE USED ON THEIR OWN IF YOU DESTRUCTURE THEM**
const BlogPostComponent = (
  **{ title, subtitle }: { title: string, subtitle?: string }**
) => {
  return(
    <div>
      <p>{**title**}</p>
      <p>{**subtitle**}</p>
    </div>
  )
}
```

现在，您可以引用具有唯一变量名的`props`值。

👋我希望这在你寻找新的学习材料时对你有用。感谢您花时间阅读这篇文章！

如果你从我的文章中更好地理解了一些东西，你可以请我喝杯咖啡来支持我:[https://www.buymeacoffee.com/daanworks](https://www.buymeacoffee.com/daanworks)

📯订阅我的电子邮件列表，如果你想在未来读到更多这样的故事，请在这里或在 [Twitter](https://twitter.com/daanwords) 上关注我！