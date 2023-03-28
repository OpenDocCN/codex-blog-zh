# 需要了解 Swift UI 中的属性包装器。

> 原文：<https://medium.com/codex/need-to-know-property-wrappers-in-swift-ui-bc9f4ce6e328?source=collection_archive---------9----------------------->

![](img/1e7475c4540eae2fc533982c95fb43a4.png)

[Jexo](https://unsplash.com/@jexo?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

属性包装器是 Swift UI 编码的基础部分。当您开始使用 Swift UI 创建您的第一个应用程序时，您会遇到几个属性包装器，需要利用它们来实现您正在寻找的功能。在后面的文章中，我们将深入探讨几个属性包装器，但是今天我们将重点关注@State、@ObservedObject、@Binding 和@Published 属性包装器。

## **状态属性包装:**

状态属性包装器是一个非常强大的属性包装器，允许您的代码以比原生 Swift 代码更具反应性的方式运行。Swift UI 使用状态来管理您声明的任何属性的存储。“State”实例不是属性本身，而是一种读写值的方法。如果您希望访问值本身，您可能希望使用它的变量名来访问包装的属性。当您将一个变量声明为@State 变量时，您让应用程序知道当变量的状态改变时，UI 也应该改变以反映新的值。这将允许您的 UI 自动刷新新存储的数据。使用@State 变量的一个常见例子是在使用 TextField 时。

```
struct MyView: View {
   @State text = "" var body: some view {
      TextField("Enter name...", text: $text)
   }
}
```

此示例创建一个变量，并将其声明为@State 对象。当用户输入文本时，UI 将根据更改进行刷新，并将值发送回变量“text”。需要注意的是，状态变量必须在视图本身中声明。当写入状态变量时，您还需要将控制器绑定到变量本身。在上面的例子中，我们通过使用“$”符号将文本字段绑定到状态变量“text”。

## **观察对象和发布的包装:**

ObservedObject 包装器用于管理外部对象的状态。ObservedObject 包装器在 Swift UI 中直接使用 ObservableObject 协议。要利用 ObservedObject 包装器，首先要创建一个符合 ObservableObject 协议的类，方法是在类名后声明它:

```
class TestClass: ObservableObject {
   @Published var views = 0
}
```

在 ObservableObject 中，您将希望用@Published 属性包装器包装要观察的任何变量。Published 属性包装器让视图知道，只要这个变量的值改变，它就应该更新。这方面的一个例子如下:

```
struct MyView: View {
   @ObservedObject var testClass: TestClass var body: some view {
      Button("Increase Views") {
         testClass.views += 1
      }
   }
}
```

在这个例子中，我们创建了一个 ObservableObject 类的变量“TestClass ”,以便在我们的视图中使用。然后，我们在按钮中访问该类中的变量，以增加视图计数。由于这是 ObservableObject 中的一个已发布变量，我们的视图知道重新加载自己以反映新的视图计数。

## **装订包装:**

绑定属性包装器是我们今天要讨论的最后一个重要的属性包装器。这个包装器的主要功能是让声明一个对象来自另一个文件，并且应该在两个位置之间共享。绑定属性包装不同于 ObservedObject，因为它不像 ObservedObject 变量那样通过引用传递对象。这方面的一个例子如下:

```
struct TestView: View {
   @State var isShowing = true var body: some view {
      VStack {
         Text("Something something code")
      }.sheet(isPresented: $isShowing) {
         SecondView(bindingBool: $isShowing)
      }
   }
}struct SecondView: View {
   @Binding var bindingBool: Bool var body: some view {
      Button("Close") {
         bindingBool = false
      }
   }
}
```

如果我们检查上面的代码，我们可以看到我们创建了两个不同的视图。第一个视图“TestView”有一个状态变量，该变量的布尔值被设置为 true。这个布尔值让我们的视图知道，如果它被设置为 true，它应该显示第二个视图。好的，一旦第二个视图打开，我们希望能够将 boolean 转换为 false，这样我们就可以返回到上一个视图。我们可以通过在第二个视图中创建一个布尔类型的绑定变量来实现这一点。在第二个视图按钮中，我们将 bool 设置为 false。现在，当我们从第一个视图中调用第二个视图时，我们将绑定布尔值设置为原始布尔值“isShowing”。因为这是一个绑定变量，当它改变它的值时,“isShowing”布尔值也会改变为 false。

# **结论:**

这就结束了我们今天关于一些重要属性包装器的文章，在开始您的 Swift UI 之旅时，您会想知道这些属性包装器。如果你还没有看过，请务必点击查看本系列的第一篇文章[。如果您喜欢我的文章，请关注我的页面，并关注更多有趣的 Swift UI 文章！](https://abeck-lb.medium.com/understanding-hstack-vstack-and-groups-in-swift-ui-e2f2e138f0e6)