# 在 iOS 上使用 Apple MapKit 实现位置共享

> 原文：<https://medium.com/codex/implement-location-sharing-with-apple-mapkit-on-ios-2659bfb0994d?source=collection_archive---------4----------------------->

![](img/816b4dc56b64b41567f90fdd20e5e080.png)

仙鸟 2022

## 如何检测用户的地理坐标&在聊天中显示一条位置消息，该消息显示一张带有位置大头针的地图

解决方案工程师| [Sendbird](https://www.sendbird.com/)

*有关本教程材料的其他指导，请参见* [*文档*](https://sendbird.com/docs/chat) *。要查看 Sendbird 聊天的运行情况，请参见此* [*演示*](https://sendbird.com/demos/in-app-chat) *。您也可以访问我们的* [*网站*](https://sendbird.com/features/chat-messaging) *了解更多关于仙鸟聊天的信息。*

# 介绍

位置共享是移动设备上最有用的功能之一。使用拼车服务时，您可以共享您的位置以确保更高的安全性，通过按需服务传达所需送货地点的位置，或者只是让朋友知道您的位置。

本教程讲解如何使用 [**Sendbird**](https://www.sendbird.com/) 和苹果的 [**MapKit**](https://developer.apple.com/documentation/mapkit/) 在 iOS 上实现位置分享功能。在本教程中，我们假设您已经使用 Sendbird 实现了 [**聊天**](https://sendbird.com/features/chat-messaging) 。如果没有，请参见本 [**教程**](https://sendbird.com/developer/tutorials/build-chat-uikit-ios) 或 [**文档**](https://sendbird.com/docs/chat/v4/ios/getting-started/send-first-message) 了解如何做到这一点。本教程提供的代码是对 Sendbird 的基本 [**Swift 示例**](https://github.com/sendbird/sendbird-chat-sample-ios) 的修改。注意，如果你了解 [**Swift**](https://developer.apple.com/swift/) ，你将从本教程中获得最大收益。

在深入讨论实现细节之前，我们先来讨论一些先决条件。

# 用苹果地图工具包在 iOS 上建立位置共享的前提条件

对于本教程，您需要:

1.  [**仙鸟 SDK**](https://github.com/sendbird/sendbird-chat-sdk-ios)
2.  [**地图工具包**](https://developer.apple.com/maps/web/)
3.  [**示例代码**](https://www.dropbox.com/s/f4i12jvq299jmw3/sendbird_sample_with_location_sharing.zip?dl=0)a .下载→解压
    b .在根文件夹中，如果需要的话，“pod install”
    c .在 AppDelegate 中，添加 app_id
    d .在 AppDelegate 中，删除“return”语句
    e .打开“send bird-IOs . xc workspace”
    f .运行
4.  搜索“//LOCATION_BUILDER”查看相关代码。

# 使用 Apple MapKit 共享位置的过程

让我们首先从较高的层面解释发送带有位置 pin 的地图的必要步骤。

用户按下“分享位置”按钮后，就会获得用户的地理坐标。该位置将通过“自定义类型”设置为“位置”的“用户消息”发送。成功发送消息后，它将被插入到消息视图委托中。根据 MessageType(用户消息)和 CustomType(位置)，消息将显示一个地图，地图坐标上有一个大头针，地图半径设置得足够小，可以清楚地显示位置。在上面分享的代码中，搜索“//LOCATION_BUILDER”找到位置分享代码所在的位置。

# 苹果地图工具包在 iOS 上的位置共享实现

要使用位置共享，您需要获得使用用户位置的权限。这将需要在运行时请求。info.plist 需要以下设置:

检测用户的位置包括两个部分。首先，开始检测用户的位置。

第二，监听何时找到用户的位置，然后发送 Sendbird 消息。

最后，将位置共享中收集的坐标作为消息文本发送，并将消息的 customType 设置为“location”。请注意，下面的代码过于简化了示例中的内容。

考虑显示传入和传出邮件的左右邮件视图。代码提供了两个。xib 文件和相关的 TableViewCell 类来显示地图消息。其概念是读取消息，检测它是否具有 customType 位置，然后呈现正确的消息视图。在此过程中，还要设置消息的地图视图坐标和区域半径。

# 结论

就是这样！我们讨论了如何获取位置坐标，将位置共享中收集的坐标作为消息文本发送，以及使用位置大头针渲染地图。您现在知道如何使用 Apple MapKit 为您的移动应用程序构建位置共享功能了！

如需更多信息，请查看 [**文档**](https://sendbird.com/docs/chat/v4/ios/getting-started/send-first-message) ，在 [**仙鸟社区**](https://community.sendbird.com/) 发布实施问题，或 [**联系我们**](https://sendbird.com/contact-us) 。我们总是乐意帮忙！

位置分享快乐！✌️