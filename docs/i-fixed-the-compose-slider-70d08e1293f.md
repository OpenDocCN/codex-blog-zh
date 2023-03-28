# 我修复了撰写滑块

> 原文：<https://medium.com/codex/i-fixed-the-compose-slider-70d08e1293f?source=collection_archive---------3----------------------->

## 我的第一个开源 Android 库

> 当谷歌给你错误的组件时，你可以自己组装一个。

![](img/901d7036e465fe24414002a277857813.png)

[来源](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.shutterstock.com%2Fsearch%2Fman%2Bthumbs%2Bup%2Bcomputer%3Fimage_type%3Dphoto&psig=AOvVaw2Ipjxy8mRdkM7IqrATGj_3&ust=1654006943878000&source=images&cd=vfe&ved=0CA4Q3YkBahcKEwi4vdKWtof4AhUAAAAAHQAAAAAQMg)

在开发一个身体质量指数计算器应用程序来练习使用 Jetpack Compose 时，我注意到 Compose 的`Slider`并没有像预期的那样工作。我尝试创建自己的可组合组件并修复了这个问题。

![](img/7ab70bc6e66a57dd8eb8b5f96146a3e6.png)

Jetpack 撰写的当前滑块有问题

我希望你能看到滑块的问题。当我试图向左滑动旋钮时，它与我拇指的位置不同步。

我很努力地在网上搜索，但是我不能找到一个合适的方法来解决我的问题。因此我决定创建一个 Android 库来解决这个问题。

[](https://github.com/cybercoder-naj/sliding-bar) [## GitHub-cyber coder-naj/sliding-bar:Slider Composable for the Jetpack Compose UI-toolkit for…

### 查看版本和变更日志滑块可用于 Jetpack 编写 Android 应用程序开发工具包…

github.com](https://github.com/cybercoder-naj/sliding-bar) 

# 我是怎么做到的？

我的逻辑是相当简单的，当谈到绘制设计。当你需要添加与视图的触摸交互时，主要的脑力就来了。我从可组合的`Canvas`开始，画了两条重叠的线和两个重叠的圆。

为了实现用户的触摸输入，我在函数传递的`modifier`上增加了`pointerInteropFilter`。基于用户何时将手指按在旋钮上(ACTION_DOWN)，用户何时拖动旋钮(ACTION_MOVE)，以及用户何时抬起手指(ACTION_UP)，我执行了一定的计算，并将`value`的新状态发射回父节点。

> 您可以在这个[链接](https://github.com/cybercoder-naj/sliding-bar/blob/main/sliding-bar/src/main/java/me/nishant/sliding_bar/SlidingBar.kt)上查看完整的代码。

# 装置

将 JitPack URL 添加到您的`settings.gradle`文件中:

```
repositories {
  maven { url 'https://jitpack.io' }
}
```

并将以下依赖项添加到您的`build.gradle`文件中:

```
dependencies {
  implementation "com.github.cybercoder-naj:sliding-bar:$version"
}
```

# 用法/示例

记得给你的类/函数添加`@ExperimentalComposeUiApi`注释。

```
SlidingBar(
    value = value,
    onValueChanged = { value = it },
    modifier = Modifier
        .padding(horizontal = 64.dp)
        .fillMaxWidth(),
    colors = SlidingBarDefaults.colors(
        colorPrimary = Color.Green,
        colorTrack = Color.Blue
    ),
    valueRange = 0f..30f,
    stepSize = 1f
)
```

演示可在[这里](https://github.com/cybercoder-naj/sliding-bar/blob/main/images/demo.gif)获得。

# 结论

我没有看到我的代码的有效解决方案；因此，通过一点数学计算，我创建了一个版本，它似乎可以根据我的需求完美地工作。

欢迎您为我在 GitHub 上的开源项目做出贡献。任何贡献都将受到感谢。贡献不分大小。

我希望你喜欢读我的文章。谢谢大家！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) profile.
My [Portfolio](https://cybercoder-naj.github.io) website.
```