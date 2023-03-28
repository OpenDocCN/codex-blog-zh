# SwiftUI 糖饼干

> 原文：<https://medium.com/codex/swiftui-sugar-cookies-55e306126c56?source=collection_archive---------9----------------------->

开始使用这个框架真的是一种新鲜空气。把东西放在一个更直观的格式中是很好的，但是仍然有一些我发现缺少的东西。

![](img/b60b8a32925edd16756ac8263276104c.png)

由[伊森·赛克斯](https://unsplash.com/@e_sykes?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

当我建造森林的时候

我发现自己在仰望黑暗无星的天空的深渊

这些结构被我的手修改过，以至于我用这样的手几乎看不到指南针

需要教皇化。

啊，是的，我记得我意识到我迷路的那一刻。我抬头看着纠结的杂草丛生的修饰语，心想，*我能在不伤害这些美丽的树状结构的情况下切掉什么呢*。突然我的眼睛开始适应。我能够看到这些修改器的模式在上面的树冠中多次出现。

丑陋的事实是，这些修饰体会增长到超出我们的理解，并导致以太中的复杂性和不和谐。问题是它们作为一种功能的局限性。

```
struct Tree: View {
    var viewModel: TreeVM
    **var** body: **some** View {
        Image("A_nice_pic")
           if viewModel.isScaledToFit {
               .scaledToFit()
           }
    }
}
```

上面的代码例子只是我通过显示一个愚蠢的修改来解决这个问题。我实际上不能在条件内调用这个函数，我建议不要在家里这样做。

那是我那时需要弄清楚的；我如何有条件地分解修饰符，这样我就可以只在`isScaledToFit`为真时调用这个函数？

哦，我的眼角有一丝微弱的光。我开始朝它前进，因为我确信我知道这将在哪里结束！

![](img/5ee95cc80404bf416f09b1f80479c5d6.png)

[Rohan G](https://unsplash.com/@rohan_g?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

最后点菜点菜点菜！

我开始打开橱柜，翻看 Swift 提供的各种配料。 *hmmm 条件句*，我们有经典的 if-else 语句，然后 switch 取值，然后立刻查看一堆可能性。我也可以设置一个枚举，然后呢？

有趣的是，这些修饰符看起来好像可以放入 switch 语句中。如果我有一个类似这样的枚举呢？

```
enum TreeOption {
    case scaledToFit
    case resizable
}
```

我绝对可以用这些代码工作。我认为真正有趣的是，如果我有一个这种类型的数组，那么它可以和 switch 语句一起使用。

```
struct TreeImage: View { let options: [TreeOption]
    // more variables and init
    ... var body: some View {
        // some beautiful code here!
            ...
     }
}
```

万岁。菜谱已经准备好了:一部分是枚举，一部分是视图，但是这里还需要一些东西来完成所有的事情。现在我可以使用带有选项的 switch 语句，修改视图，然后返回它！

## 这里有一个函数！

```
func modifiedByOption(_ view: Image, with type: TreeOption) -> Image {
    switch type {
    case .scaledToFit:
        return view.scaledToFit() 
    case .resizable:
        return view.resizable()
    } 
}
```

现在`TreeImage`可以运行选项，然后调用`modifiedByOption`,可以调用并返回修饰符，非常类似于使用自定义修饰符在视图上链接多个修饰符的概念。(它可以是视图私有的，或者更好的是视图协议的默认实现)

现在我可以做几件事。首先，我可以使用视图并在一个数组中设置选项`[.resizable]`(更多的情况下，这看起来更好)，或者我可以将这个数组设置为一个常量变量并使用它(这将再次清理更复杂的情况)。所以看起来有点像`TreeImage("image_name", ImageConstants.resizableImage)`。

这里的美妙之处在于枚举器的关联值突然可以设置数组来反映对 UI 的任何更改。也许这个数组也可以被设置成一个状态，这样，如果没有观察到任何变化，它就可以防止在每次绘制时被实例化。

![](img/8bb34538dfbfb2e3f786ee59530633ff.png)

照片由[丹尼斯·德吉奥安尼](https://unsplash.com/@denisdegioanni?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

这些结构被捆绑在一起，突然间这些冗余被清理掉了！

我可以再次看到天空中的威严，我可以轻松地驾驭自己的作品。我最大的希望是其他人也有这样一个容易的时间，并可以大踏步地加强 SwiftUI。

目前，这意味着压缩某些视图，并拥有更干净的本地视图，可以用常量设置这些视图，以创建无处不在的样式和更友好的代码来共享。然而，这只是一个表面配方，可以帮助处理这些条件问题，收紧冗余。在我看来，我们有这个视图堆栈，每个视图都有一个修饰符链，我会说这些修饰符被翻译成画图指令，在结构喜欢构建的堆栈中运行，但不是创建这些指令并复制视图(我相信修饰符在幕后就是这样做的)。我们用选项字典实例化该结构，每个类型都映射到一个 api 调用，以及需要设置的任何需要进行复制的内容。

遗憾的是，这无助于解决秩序问题。也许有一天，类型可以携带重量，自动排序为一个更好的秩序。我在这里所说的一个很好的例子。取一个文本视图，修改框架高度到一个明显的大小，改变背景颜色(很好地填充框架高度对吗？)，现在把那些功能反过来。你应该看到它们产生了两种完全不同的结果。我认为这部分是由于修改器复制自身。