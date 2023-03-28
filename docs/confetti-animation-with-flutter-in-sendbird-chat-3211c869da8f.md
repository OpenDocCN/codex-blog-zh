# Sendbird 聊天中带飘动的五彩纸屑动画

> 原文：<https://medium.com/codex/confetti-animation-with-flutter-in-sendbird-chat-3211c869da8f?source=collection_archive---------8----------------------->

![](img/beeef6d32dc054181c98ba38ae9febf9.png)

仙鸟 2022

## 如何在用户收到祝贺消息后显示一阵五彩纸屑

由谷嘉诚
解决方案工程师| [森伯德](https://www.sendbird.com/)

*你可能会发现检查一下 Flutter* [*样本 app*](https://github.com/SendbirdCommunity/sendbird-confetti-flutter-sample) *很有用。有关本教程中材料的其他指导，请参见* [*文档*](https://sendbird.com/docs/chat/v3/flutter/quickstart/send-first-message) *。要查看仙鸟聊天的运行情况，请查看这个* [*演示*](https://sendbird.com/demos/in-app-chat) *。您也可以访问我们的* [*网站*](https://sendbird.com/features/voice-and-video) *了解更多关于 Sendbird Calls 可以提供的服务。*

# 介绍

chat 中一个非常受欢迎的功能是五彩纸屑动画，当聊天消息中收到单词“恭喜”或“恭喜”时，会显示一阵五彩纸屑。我们之前在这篇 [**文章**](https://sendbird.com/blog/using-confetti-animation-in-sendbird-uikit) 中记录了如何为 React 应用程序实现五彩纸屑动画。在本教程中，我们将讨论如何用 Flutter 实现这个特性。

本文中的代码是之前使用 Sendbird 的 [**Flutter SDK**](https://github.com/sendbird/Sendbird-Flutter) 和第三方聊天 UI 插件 [**DashChat**](https://pub.dev/packages/dash_chat_2) 创建的 Flutter 示例应用程序的进展。如果这是你第一次在一个 Flutter 应用程序中实现 [**Sendbird Chat**](https://sendbird.com/features/chat-messaging) ，我们建议你先看看这个 [**教程**](https://sendbird.com/developer/tutorials/sendbird-flutter-3rd-party-ui) 来了解如何用 Sendbird 和第三方 ui 构建一个 Flutter 聊天应用程序。

在本教程中，我们将首先看到如何创建一个基本的特效类，然后是一个显示五彩纸屑的自定义子类，最后是如何添加一个控制器来保存和运行我们所有的特效子类。本教程的完整代码旨在允许快速集成到现有项目中，并灵活地添加/删除您自己的自定义效果，对任何现有代码的影响最小。

请注意，如果你以前有过一些使用 Flutter 的经验，你将会从本教程中受益匪浅。

我们开始吧！💻

# 要求

*   [](https://docs.flutter.dev/get-started/install)**安装完毕≥2.5**
*   **[T5【仙鸟】SDK包](https://pub.dev/packages/sendbird_sdk)**
*   **[**DashChat 2**](https://pub.dev/packages/dash_chat_2) 插件**
*   **[**五彩纸屑**](https://pub.dev/packages/confetti) 插件**

# **体系结构**

**为了保持我们的代码相对简单、可移植和可扩展，我们将从存储自定义对象列表的 Flutter 控制器对象开始。这个控制器可以由构建用户界面(UI)的任何有状态或无状态小部件来初始化和保留。**

**当聊天消息传递到这个控制器时，控制器会将消息传递到它存储的自定义子类列表中。这些子类中的每一个都将检查特定关键字的消息，如果找到，就播放它的效果。然后，他们会指出用户已经看到了动画，并更新其 Sendbird 消息元数据，以便当用户重新打开聊天屏幕时，不会出现相同的效果。**

**现在让我们深入了解实现细节。**

# **第一部分。基础类**

**首先，创建一个名为 sendbird_sfx.dart 的文件，这将是我们的基本特效类。它将具有以下功能:**

*   **返回 UI 小部件**
*   **开始一个效果**
*   **停止效果**
*   **处理任何与运行效果相关的对象**

**要支持这些功能，请添加以下属性:**

*   **元键—区分特效的唯一键**
*   **关键词—触发特殊效果的关键词列表**
*   **isCaseSensitive —设置是否应检查上述关键字列表是否区分大小写。False 表示将忽略大小写。**
*   **expiresIn —一个持续时间属性，用于设置此效果应适用多长时间的阈值。比如你想在有人发“周年快乐！”但如果消息最初是在一个月前发送，则持续时间可以设置为一个月内。**

**现在，让我们添加一个构造函数，以确保在 init 期间提供所需的属性:**

**添加以下应被未来子类覆盖的函数:**

**以下是可以选择性覆盖的代码块:**

**复制以下内部函数来处理样板操作并与 Sendbird 的系统同步:**

**参见 [**Github**](https://github.com/SendbirdCommunity/sendbird-confetti-flutter-sample/blob/master/lib/sendbird_sfx.dart) 获取 sendbird_sfx.dart 类的完成代码。**

# **第二部分。自定义子类**

**现在我们将创建 SendbirdSFX 的一个子类，用于在检测到关键字“恭喜”或“祝贺”时显示五彩纸屑。**

**创建一个名为 confetti_sfx.dart 的新类，确保它扩展了上面的基类。**

**为了实现这个效果，我们将使用开源的 [**五彩纸屑包**](https://pub.dev/packages/confetti) 。在您的 pubspec.yaml dependencies 部分添加了 confetti 之后，添加 import ' package:confetti/confetti . dart '；到 ConfettiSFX 类。**

**将五彩纸屑包初始化为一个属性以保留它:**

```
final ConfettiController _confettiController = ConfettiController(duration: const Duration(seconds: 10));
```

**然后在 ui()函数的覆盖中添加它作为包的 confetti 小部件的控制器:**

**现在，向 play、stop 和 dispose 函数添加 _confettiController 调用:**

**confetti_sfx.dart 类的完整代码可以在 [**Github**](https://github.com/SendbirdCommunity/sendbird-confetti-flutter-sample/blob/master/lib/confetti_sfx.dart) 上找到。**

# **第三部分。添加控制器**

**接下来，创建另一个名为 sendbird_sfx_controller.dart 的文件。这是一个方便的类，用来保存和运行我们所有的特效子类。添加导入语句:**

```
import "sendbird_sfx.dart";
```

**这样我们就可以添加单个列表属性:**

**列出<sendbirdsfx>特殊效果；</sendbirdsfx>**

**添加一个构造函数，以便在初始化时选择性地传入一个特效子类列表:**

```
SendbirdSFXController({this.specialEffects = const []});
```

**现在创建以下函数来传递消息字符串，并向它和它的每个特殊效果子类传递 dispose 命令:**

**sendbird_sfx_controller.dart 类的完整代码可以在 [**Github**](https://github.com/SendbirdCommunity/sendbird-confetti-flutter-sample/blob/master/lib/sendbird_sfx_controller.dart) 上找到。**

**最后，我们将把这些类添加到前面示例中的 group_channel_view.dart 类中，以便与通过 Sendbird 发送和接收的消息结合起来。**

**添加这些导入语句:**

**然后在 _GroupChannelViewState 块中，添加一个新属性来保留我们的 SendbirdSFXController:**

**我们不会修改现有的 body()块，而是将它封装到一个 stack 小部件中，这样我们就可以在它上面添加所有的特效 UI。创建一个 sfxStackedBody 函数，该函数使用以下参数返回一个小部件:**

**然后更新 build()函数以调用此块而不是原始的 body 块，传入 _sfxController 属性、预先存在的 _messages 属性和 Sendbird 组通道引用:**

**现在，当类的 _messages 属性被更新并启动 UI 重建时，更新的消息将由 _sfxController 处理，以确定是否应该触发五彩纸屑效果。如果一个单词与关键字列表中的一个匹配，五彩纸屑效果将播放该效果。**

**注意:这是集成消息检查的最简单的方法，但不是最有效的，因为过去的消息总是会被重新检查。实现 onMessageRecieved()和 onMessageUpdated()回调(更多信息 [**此处**](https://sendbird.com/docs/chat/v3/flutter/guides/event-handler#4-list-of-group-channel-events) )并将 SendbirdSFXController 的 checkAndTriggerAll() all 函数移至这些函数。**

**更新后的 group_channel_view.dart 类的完整代码可以在 [**Github**](https://github.com/SendbirdCommunity/sendbird-confetti-flutter-sample/blob/master/lib/group_channel_view.dart) 上找到。**

# **结论**

**就是这样！你做到了！🚀现在你已经知道如何添加五彩纸屑到你的 Flutter 应用程序，你可以制作你自己的聊天触发的效果——其他动画，音频效果，甚至其他代码块！**

**享受体验你自己的聊天触发的特殊效果！开心聊楼！✌️👨‍💻**