# 在您的撰写项目中停止这样做

> 原文：<https://medium.com/codex/stop-doing-this-in-your-compose-project-4382f2d3b768?source=collection_archive---------11----------------------->

![](img/64d8c57504d45117e4187d70e9a9771e.png)

您正在创建一个非常棒的可组合函数。它有一个复杂的用户界面，有吸引人的动画和大量的定制空间。你花了几个小时在这个功能上，试图使它完美。但是，哦，它有太多的定制参数。

```
@Composable
fun MyFancyComponent(
  value: Int,
  onValueChanged: (Int) -> Unit,
  modifier: Modifier = Modifier,
  primaryColor: Color = Color.Black,
  accentColor: Color = Color.Red,
  itemSpacing: Dp = 8.dp,
  componentStyle: ComponentStyle = ComponentStyle.Normal,
  [...]
) {...}
```

虽然有些人更喜欢对他们的组件使用这种方法，并使用命名的参数调用函数，但有些人不喜欢让他们的参数列表变得臃肿，而是希望找到一种替代方法。类似的工作方式。

`Modifier`是一个接口，您可以在其中链接您希望布局可组合的属性，以满足您的设计。让我们尝试将这种技术用于`MyFancyComponent`功能。

在您的项目中，让我们创建一个类并将其命名为`MyFancyComponentAttributes`。这将具有您需要的可组合函数的所有属性。对于您希望添加的每个属性，用一个默认值和一个私有 setter 保持它的公共属性，并添加一个与属性同名的函数。

例如，我们已经创建了类，我们想添加`primaryColor`属性，我们可以写，

```
class MyFancyComponentAttributes { var primaryColor: Color = Color.Black
    priavte set fun primaryColor(value: Color) = 
    apply {
      primaryColor = value
    } [...]
}
```

该函数将接受一个颜色作为参数，并返回具有修改属性的相同对象。对所有属性继续这样做。

现在将可组合函数改为

```
/* in your composable file */
@Composable
fun MyFancyComponent(
  value: Int,
  onValueChanged: (Int) -> Unit,
  modifier: Modifier = Modifier,
  attrs: MyFancyComponentAttributes = MyFancyComponentAttributes()
) {
  // Access properties by attrs.primaryColor, etc
}/* while calling the composable function */
MyFancyComponent(
  value = number,
  onValueChanged = { number = it},
  modifier = Modifier
    .padding(horizontal = 16.dp),
  attrs = MyFancyComponentAttributes()
    .primaryColor(Color.White)
    .accentColor(Color.Purple)
    .itemSpacing(12.dp)
)
```

这是一种简单而强大的方法，可以将必要的属性从阻塞的函数定义中分离出来，并且与 Google 提供的`Modifier`模式一致。