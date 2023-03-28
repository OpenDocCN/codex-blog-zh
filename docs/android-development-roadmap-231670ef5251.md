# Android 开发路线图

> 原文：<https://medium.com/codex/android-development-roadmap-231670ef5251?source=collection_archive---------3----------------------->

![](img/6424fb1b00a926e3c89baeaeb91c6433.png)

费伦茨·阿尔马西在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

# 安卓是什么？

根据维基百科，

> “Android 是一个基于 Linux 内核和其他开源软件的修改版本的移动操作系统，主要为智能手机和平板电脑等触摸屏移动设备设计。”

简单来说，Android 是一个专门为手机和触摸屏设备打造的操作系统。Android 是世界上最受欢迎的操作系统，在 190 多个国家拥有超过 25 亿活跃用户。

# 如何在 Android 中开始开发？

# 选择一种语言

对于语言，你可以使用 Kotlin 或者 Java 来开发 android 应用。虽然，你可以同时使用 [Kotlin](https://en.wikipedia.org/wiki/Kotlin_(programming_language)) 和 [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) 来开发原生 android 应用，[谷歌在 2019 年宣布](https://android-developers.googleblog.com/2019/05/google-io-2019-empowering-developers-to-build-experiences-on-Android-Play.html)让 Kotlin 成为开发 android 应用的首选方式。如果你今天开始学习 android 开发，Kotlin 更受欢迎，但这完全取决于个人的偏好。

## 版本控制系统

版本控制系统记录您对代码库的更改，并允许您在以后调用特定的版本。有多种版本控制系统可用，但 Git 是目前最常用的一种。

# 构建应用程序

要开始构建应用程序，首先你必须安装[**Android Studio**](https://developer.android.com/studio)[**JDK**](https://www.oracle.com/java/technologies/downloads/)**Android SDK**等等。这些是构建您的第一个 android 应用程序所需的一些工具。

**Android Studio** 是谷歌 Android 操作系统的官方集成开发环境，基于 JetBrains 的 IntelliJ IDEA 软件构建，专为 Android 开发而设计。你应该熟悉 android studio 和 android 项目的文件结构。

## **安卓组件**

**活动:**

*   活动生命周期
*   处理活动状态更改
*   了解任务和后台堆栈
*   流程和应用程序生命周期

**服务:**

*   Android 服务的类型
*   Android 服务的生命周期

**内容提供商:**

*   内容 URI
*   内容提供商的运营
*   内容提供商的工作
*   创建内容提供商

**广播接收器:**

*   隐式广播异常

## UI 设计

在你对 Android 的不同组件有了一个概念之后，开始探索一些简单的 UI 设计

XML 在 Android 项目中主要用于 UI 设计。android SDK 已经提供了各种 UI 组件，可用于开发任何 android 应用程序的 UI。

## 应用清单

每个 Android 项目都必须有一个 **AndroidManifest.xml** 文件，它描述了关于应用程序的基本信息，比如包名、入口点、组件、权限和元数据。

## 包名和应用程序 ID

每个 Android 项目都必须有一个**包**属性，它在 **AndroidManifest.xml** 文件中代表您的应用程序的通用惟一应用程序 id。清单文件必须位于您的 **/src/main** 文件夹中。

# 结论

Android 开发路线图的第一部分到此结束。这篇博客涵盖了 android 开发的重要细节。

谢谢大家！