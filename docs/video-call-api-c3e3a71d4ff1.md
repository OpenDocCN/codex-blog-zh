# 如何使用视频通话 API 搭建视频通话直播 app

> 原文：<https://medium.com/codex/video-call-api-c3e3a71d4ff1?source=collection_archive---------8----------------------->

## 本文将介绍如何使用 ZEGOCLOUD 的视频通话 API 和 firebase 来构建一个视频通话直播 app

![](img/272d9c98a495169e0eda8bb46e231a8c.png)

## 介绍

随着设备性能和网络质量的不断提升，越来越多的音视频通话场景得到应用，视频通话 API 也得到越来越多的应用。

视频通话 API 可以提供哪些能力？

随着视频通话 API 的需求越来越大，提供视频通话 API 的公司也越来越多，但提供的功能大同小异。

视频呼叫 API 提供的功能主要包括:

1.  通话邀请
2.  语音和视频通话
3.  离线推送功能
4.  呼叫用户界面和交互

下面是使用 ZEGOCLOUD 的视频通话 API 搭建的视频通话直播 app。

![](img/df6316f2a41f56299ecb21122fbec7d2.png)

## ZEGOCLOUD 的视频通话 API 是什么？

ZEGOCLOUD 的视频调用 API 采用分层架构。详情如下图所示:

![](img/e7fa75acb5dd49be34ff8271cf9d8f14.png)

ZEGOCLOUD 的视频调用 API 为你提供了两种不同的 SDK:`CallUIKit`和`Call SDK`。

`CallUIKit`:提供完整的视频直播通话 UI，只需要调用通话界面，在需要完成通话过程的地方挂机界面即可。

`Call SDK`:提供视频直播通话的底层逻辑，如音视频管理、通话管理、网络状态管理等。你只需要专注于上层自定义 UI 的实现，调用相应的交互接口就可以完成调用过程。

## 如何使用 ZEGOCLOUD 的视频通话 API

## 第一步。创建一个 ZEGOCLOUD 帐户

*   在 [ZEGOCLOUD 官方](https://www.zegocloud.com/account/signup?_source=medium&article=25)创建账号。

![](img/4bb7bebbd4b686d8875d83284f375d56.png)

## 第二步。创建新项目

*   在 [ZEGOCLOUD 管理控制台](https://console.zegocloud.com?_source=medium&article=25)中创建一个项目。

![](img/62e7b23751558dd96ebd6703afeb3ad5.png)

## 第三步。创建一个 Firebase 项目

在 [Firebase 控制台](https://console.firebase.google.com/)中创建一个 Firebase 项目。更多详情，请参见 [Firebase 文档](https://firebase.google.com/docs/functions/get-started)。

![](img/f0d042e03ee8c66a69e3ea7f75022f38.png)

## 第四步。部署 Firebase 云功能

ZEGO Call 默认使用 Firebase Cloud 函数作为业务服务器，我们建议您在集成 ZEGOCall SDK 之前激活并部署它。

1.  在 Firebase 中创建一个新的实时数据库。

![](img/f1da5336474ace0b14dd870bae9b6de9.png)

1.  通过添加以下内容来编辑实时数据库的规则:

```
{
  "rules": {
        ".read": "auth.uid != null",
        ".write": "auth.uid != null",
  }
}
```

![](img/318d720cfa1eccce62b7cf8f6d995174.png)

1.  通过 npm 安装 CLI。

*   `npm install -g firebase-tools`

![](img/b94291eb2e0cb0d092abcd82e7ea1cb9.png)

1.  运行`firebase login`通过浏览器登录并验证 firebase 工具。

![](img/8b20ee12113b2df7e9e568fb8955186f.png)

1.  运行`firebase init functions`。该工具为您提供了安装 npm 依赖项的选项。如果您想以另一种方式管理依赖项，拒绝是安全的，但是如果您拒绝，您需要在模拟或部署您的功能之前运行 npm install。

![](img/6b51ea4a4edbe2b4dcbd1f521a3af092.png)

1.  下载[云函数示例代码](https://github.com/ZEGOCLOUD/call_firebase_funcitons)。

![](img/25b57532ed669735af57cfdb8f58ee9c.png)

1.  将示例代码中的`firebase.json`、`functions\index.js`文件和`functions\token04`文件夹复制到您的云函数项目中，覆盖同名文件。

![](img/c11f7b081314058c6f34f73d28c5ec8a.png)![](img/45d96a980ac339cf2396d9853e8f8c57.png)

1.  修改`index.js`文件，正确填写从 [ZEGOCLOUD 管理控制台](https://console.zegocloud.com?_source=medium&article=25)获得的 AppID 和 ServerSecret。

![](img/40304d6428ac38fcfaa8940cd0897bc1.png)

1.  在 Firebase CLI 中，运行`firebase deploy --only functions`命令来部署云功能。

![](img/6480b1829d2b0e081c6849969e13825e.png)

## 第五步。将文件复制到项目中

要将 ZEGOCallUIKit 与 CocoaPods 自动集成，请执行以下操作:

1.  下载[示例代码](https://codestore.zegocloud.com/project/2)，并将`ZEGOCallUIKit`和`ZEGOCall`文件夹复制到您的项目目录中(如果您没有项目，请创建一个新项目)。

![](img/31434e58cf914ad1377ceb58cae61b3c.png)

1.  打开终端，导航到您的项目目录，运行`pod init`命令创建一个`Podfile`，然后将以下内容添加到文件中。

*   `pod 'ZegoExpressEngine' pod 'FirebaseAuth' pod 'GoogleSignIn' pod 'Firebase/Database' pod 'Firebase/Analytics' pod 'Firebase/Crashlytics' pod 'Firebase/Messaging' pod 'Firebase/Functions'`

![](img/e0b25d748db0ff0afdf3c46628a635c9.png)

1.  在终端中，导航到 Podfile 所在的目录，运行`pod install`命令。

*   `pod install`

1.  重启 Xcode，打开新生成的。工作区文件。

## 第六步。添加权限

可以根据需要设置权限。

1.  打开 Xcode，选择目标对象，然后点击**信息>自定义 iOS 目标属性**。

![](img/2719d9c05db016fbd0bdbf80cb15a3b7.png)

1.  点击**添加按钮(+)** 添加摄像头和麦克风权限。

*   `Privacy-Camera Usage Description`
*   `Privacy-Microphone Usage Description`

![](img/e66ca48d7c4c926790933c5b8251b5fb.png)

1.  选择目标项目，选择**标志&能力>能力**，搜索**后台模式**并双击。

![](img/9d7fdff900d7e967b910aae55416eb9c.png)

1.  在**背景模式**中检查以下选项。

![](img/29f4cb2dd108e4b61502e295e60fabed.png)

1.  启用**推送通知**功能。

![](img/93181f5cf4e0223be52f69996f7d9d96.png)

1.  下载 firebase 的配置文件**Google service-info . plist**，并将其添加到您的项目中。要获取配置文件，请参考[如何获取配置文件？](https://firebase.google.com/docs/ios/setup#add-config-file)。

![](img/86dcca456ba83a38cd4ec031126863ad.png)![](img/0c88d01e41c52a6e39f3d1bca9997745.png)

1.  设置 **URL 类型**:用**Google service-info . plist**文件中的 **REVERSED_CLIENT_ID** 字段填写 **URL 方案**。

![](img/60eebe727cf221b1295bc6cc2eaa9468.png)

## 初始化 ZEGOCallUIKit

要初始化 ZEGOCallUIKit，获取`CallManager`实例，传递项目的 AppID。

要接收回调，将相应的`delegate`设置为`self`。

## 用户登录

ZEGO Call 还不提供用户管理功能。您需要让用户登录 Firebase，然后调用`setLocalUser`方法，根据登录结果将用户信息设置为 CallUIKit。

Firebase 提供了多种登录身份验证模式。下面以 Google 登录为例。
更多模式请参考 [Firebase 官方](https://firebase.google.com/docs/auth/where-to-start)。

## 获得代币

ZEGO Call 使用 RTC SDK 实现语音和视频通话功能。主叫用户和被叫用户加入同一个房间，并且在呼叫接通时开始流发布和流播放。因此，要进行出站调用，您需要为 RTC SDK 提供一个令牌进行验证。有关详细信息，请参见[使用令牌进行认证](#11648)。

在发出或接受出站呼叫之前，您需要从 Firebase Cloud 函数获取一个令牌。

为`CallManager`的 Token 属性设置一个令牌，实现`TokenProvider`的代理拥有的回调`getRTCToken`。

## 拨打出站电话

要进行出站语音呼叫，调用`callUser`方法并将`type`参数设置为`CallType.voice`。

要进行出站视频呼叫，调用`callUser`方法并将`type`参数设置为`CallType.video`。

发出呼叫后，CallUIKit 会自动为被叫方和主叫方显示当前状态的 UI(需要集成 CallUIKit)，您可以在 UI 上进行进一步的操作。

*   当调用方调用方法进行调用时，将显示以下内容(为了便于说明，进行语音调用)

![](img/143f48e2176fdb9a4377cccc158aadb7.png)

*   当被叫方收到来电时，会弹出以下黑色提示(以语音呼叫为例)

![](img/00d3844a3ac1e59fced039f0c3bc0bfe.png)

## 上传日志

当您在使用您的 app 时遇到问题，调用`uploadLog`方法上传日志，以便我们更快地定位和帮助您。

## 取消初始化 ZEGOCallUIKit

在使用完 ZEGO 调用后，调用`unInit`方法来取消 SDK 的初始化。

[与 ZEGOCLOUD 签约](https://console.zegocloud.com/account/signup?_source=medium&article=25)，每月免费获得**10000 分钟**。

# 你知道吗？👏

```
You can give up to **50 Claps** for an article?**Tap and hold the clap button** for a few seconds.**Follow me** to learn more technical knowledge.Thank you for reading :)
```

## 了解更多信息

这是现场技术文章之一。欢迎阅读其他文章:

[](https://faun.pub/how-to-build-an-android-video-call-app-with-firebase-realtime-database-af76c2b202d9) [## 如何使用 firebase 实时数据库构建 Android 视频通话应用程序

### 本文将帮助您快速构建一个视频/音频通话应用程序

faun.pub](https://faun.pub/how-to-build-an-android-video-call-app-with-firebase-realtime-database-af76c2b202d9) [](https://faun.pub/how-to-build-a-clubhouse-clone-app-with-android-and-zegocloud-a-social-audio-app-development-cb4059e361b0) [## 如何用 Android 和 ZEGOCLOUD 构建一个俱乐部会所克隆应用——一个社交音频应用开发

### 本文解释了如何使用 ZEGOCLOUD ZEGOLiveAudioRoom SDK 快速克隆一个俱乐部应用程序

faun.pub](https://faun.pub/how-to-build-a-clubhouse-clone-app-with-android-and-zegocloud-a-social-audio-app-development-cb4059e361b0) [](https://faun.pub/using-ios-callkit-7ffd260e80d4) [## 如何用 iOS CallKit 和 ZEGOCLOUD 实现一个视频通话 app

faun.pub](https://faun.pub/using-ios-callkit-7ffd260e80d4) [](https://faun.pub/video-api-fcd2bcbe24f7) [## 你了解视频 API 吗？

### 这篇文章描述了什么是视频 API？视频 API 如何保证音视频的流畅传输？

faun.pub](https://faun.pub/video-api-fcd2bcbe24f7) [](https://faun.pub/super-live-chat-136dfa19f96f) [## 如何实现超级实时聊天:你应该知道的 3 种方法

faun.pub](https://faun.pub/super-live-chat-136dfa19f96f)