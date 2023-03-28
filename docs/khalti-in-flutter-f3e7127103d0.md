# 飘舞中的卡蒂

> 原文：<https://medium.com/codex/khalti-in-flutter-f3e7127103d0?source=collection_archive---------3----------------------->

Khalti 是尼泊尔的数字支付方式之一。在这个故事中，我们将学习如何将 Khalti 整合到 Flutter 中。

# 入门指南

集成 Khalti 支付网关需要三样东西。

1.  **Khalti 账号**:为了在 Flutter 中集成 Khalti，你需要有一个只有在你有 Khalti 账号的情况下才能创建的商家账号。您可以通过访问此[链接](https://khalti.com/)并点击 ***注册*** 轻松免费创建 Khalti 帐户。
2.  **商家账户**:创建了 Khalti 账户后，你现在可以通过访问此[链接](https://khalti.com/merchant/)轻松创建商家账户。验证过程结束后，您将被重定向到管理仪表板，在这里您可以在**设置**菜单中找到密钥。
3.  安装在计算机中的颤振

# 设置

为了在 Flutter 中集成 Khalti，我们将使用包 [khalti_flutter](https://pub.dev/packages/khalti_flutter) 。在 **pubsec.yaml 文件的依赖项中添加 khalti_flutter 包。**

```
dependencies:
 ....
  khalti_flutter: ^2.1.0
```

# 机器人

在您的应用程序的`AndroidManifest.xml`中，在`<activity>...</activity>`标签中添加以下几行:

```
<meta-data android:name="flutter_deeplinking_enabled" android:value="true" />
<intent-filter android:autoVerify="true">
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="kpg" android:host="{your package name}" />
</intent-filter>
```

# ios

在你的应用程序的`Info.plist`中，添加这些属性:

```
<key>FlutterDeepLinkingEnabled</key>
<true/>
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>kpg</string>
        </array>
        <key>CFBundleURLName</key>
        <string>{your package name}</string>
    </dict>
</array>
```

# 网

网站不需要任何配置。

# 桌面

桌面不需要任何配置。

完成所有设置后，我们需要在 MaterialApp 中初始化 KhaltiScope。

如果您还没有创建商家帐户，请使用公钥

> test _ public _ key _ d5d9f 63743584 DC 38753056 b0cc 737d 5

初始化 Khalti 后，现在我们前进到点击按钮时用 Khalti 支付。

在交易的`success`之后，Khalti 提供交易的唯一 id`referenceId`。

`onFailure`

`onFailure`

# 让我们连接起来

我们可以成为朋友。在[脸书](https://www.facebook.com/nabin.dhakal.714/)、 [Linkedin](https://www.linkedin.com/in/nabindhakal/) 、 [Github](https://github.com/nbnD) 、 [Youtube](https://www.youtube.com/channel/UCW6oYt_3QSl7J2HSHNqwXWw) 和 [Instagram](https://www.instagram.com/nbn_d_/) 上查找。

拜访:[颤振结](https://flutterjunction.com/)

**投稿:** [BuyMeACoffee](https://www.buymeacoffee.com/nabindhakal)

# 结论

希望这篇文章对你有所帮助，让你学到新的东西。我在这篇文章中使用了一些对你们中的一些人来说可能是新的东西。

如果你学到了新的东西或者想提出一些建议，请在评论中告诉我。

如果你喜欢这篇文章，请点击👏图标，为您提供传递所有新事物的动力。

此外，关注令人兴奋的文章和项目的更新。

通过分享学习对学习过程产生了巨大的影响，并使社区越来越大。

分享是吸引其他爱好者的磁石。

因此，让我们朝着扩大我们的学习社区迈出一小步。

与你的朋友分享这篇文章，或者如果你喜欢这篇文章，就在推特上发表评论。

# 在以下时间吃饱:

[](https://github.com/nbnD/khalti_flutter) [## GitHub - nbnD/khalti_flutter:在 flutter 中集成 khalti

### 一个新的颤振项目。这个项目是颤振应用的起点。一些帮助您入门的资源…

github.com](https://github.com/nbnD/khalti_flutter)