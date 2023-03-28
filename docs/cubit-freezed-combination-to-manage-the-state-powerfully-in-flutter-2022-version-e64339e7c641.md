# Cubit +冻结组合，强力管理颤振状态(2022 版)

> 原文：<https://medium.com/codex/cubit-freezed-combination-to-manage-the-state-powerfully-in-flutter-2022-version-e64339e7c641?source=collection_archive---------3----------------------->

![](img/af64b7904518a23071736e2d467f3d4e.png)

在[上一部分(登录的 BLoC 模式:cubit _ log in in in Flutter)](/@sahinefe/bloc-pattern-for-login-bloc-login-in-flutter-551fd05beb99)中，我们使用 cubit 创建了一个带有 Flutter 的登录应用程序，它是 BLoC 的一个子类(我实际上是这样调用的，因为它有一些 BloC 属性)。当我们创建以前版本的登录应用程序时，我们没有使用“冻结”包。在这一部分，我们将使用它，我们将看到冻结的软件包如何为我们的状态管理提供强大的功能。

# 我们将建造什么？

我们要用冷冻的包裹重新制作腕尺。我们将使用与上一部分相同的 UI，因为我们将在这篇中型文章中关注状态管理。简单来说，我们可以说，我们会更新上一个项目的路由器和状态管理，会更好。

如果我们不使用冻结的包，我们必须在我们的主状态文件中编写所有的样板代码。在这种情况下，让我们比较冻结和不冻结包的代码。

## auth_state.dart(未冻结)

## auth_state.dart(已冻结)

这里你可以说有什么区别吗？仅仅是 7(13–6)行吗？当然不是！让我们看看冷冻包为我们做了什么(227 行，对此表示抱歉)。

所以，我们不需要做所有的事情，也不应该像上面说的那样手工修改代码。我们再看一个例子(当然没有 200 行代码)。

## login_state.dart(无冻结)

## login_state.dart(带冻结)

使用 freezed，我们创建一个构造函数，如果我们愿意，我们用 *"@Default()"* 标签给它们赋予初始值(如果您将 Freezed 导入到 *pubspec.yaml* 文件并创建带有 bloc 扩展名的 cubit，那么它将创建带有 freezed 包的默认 cubit **)，就这样！**

既然我们已经浏览了 cubits 中的基本冻结包，现在让我们看看如何正确地创建它。

# 带肘的冷冻包装

创建腕尺后，我们会有这样的代码:

因此，如果您在代码中看到(我确信您会看到)红线/错误，请不要惊慌。我们将使用 build runner 运行神奇的代码来完成冻结包的魔法。

> ***神奇代码:*** *扑扑 pub run build _ runner watch—delete—conflicted—outputs*

将此代码粘贴到终端，之后您将获得 1 个(或更多)输出。如果您不想一直打开构建运行器，您可以将*手表*更改为*构建*。

## 如果我想在状态中创建特殊的方法呢？

因此，您需要在构造函数中使用带下划线的点，比如:

> ***const SignUpState。_();***

之后，您可以创建特殊的方法，如 *SignUpState.loading()、*等。

## 丘比特呢，不是国家？

对于 cubits，我们可以使用 SignUpState.initial()作为构造函数。除此之外，当我们更新状态时，我们知道我们需要为此使用 emit。当我们使用 emit 时，我们可以很容易地通过**状态到达 **copywith** 方法。让我们看一个例子，看看 cubit 和 Freezed 的用法。**

## auth_cubit.dart

因为我们没有给变量 *isSignedIn* 一个默认值，所以我们用 *required* 关键字编写。所以我们可以给构造函数赋值。在本例中，如果用户为 null，我们说将状态(isSignedIn)更新为 false，但如果用户不为 null，则使用 true 值进行更新。很简单，对吧？

## 是时候使用 flow_builder 根据“状态”路由用户了

*flow_builder* 包根据我们的状态提供路由屏幕。例如，如果我们的用户已经登录，那么用户应该停留在主页上，反之亦然，如果用户没有登录，那么用户应该停留在登录页面上。那么，如何才能轻松使用 *flow_builder 包*？

此处更新了登录页面。当应用程序启动时，它首先在阻止提供程序后工作。我们又有了*flow builder<ourState>、pages 和 State。对于页面，我们创建静态的 final 方法来调用它们根据状态进行路由。*

> static Page Page()= > const material Page<void>(child:home Page())；</void>

# 结论

我已经在 Google Play Store 上为我的大学(伊兹密尔理工学院)创建了一个应用程序，它的名字是 cepte 西普特，当我创建这个应用程序时，我没有使用冻结的软件包，也没有使用 flow_builder。过了一段时间，我意识到它们为我们提供了动力。你可以在这里找到所有的代码。我希望这篇教程能帮助你理解你应该如何在 freezed 和 flow_builder 包中使用 cubit_login。欢迎在评论区提问。

感谢您的阅读，敬请关注！

干杯。

![](img/8715be236e11811a791be3c3bf94b1f4.png)