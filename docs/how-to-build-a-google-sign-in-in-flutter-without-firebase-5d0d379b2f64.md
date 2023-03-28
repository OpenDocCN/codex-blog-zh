# 如何在没有 Firebase 的情况下在 Flutter 中构建一个 Google 登录

> 原文：<https://medium.com/codex/how-to-build-a-google-sign-in-in-flutter-without-firebase-5d0d379b2f64?source=collection_archive---------1----------------------->

使用 [Flutter AppAuth](https://pub.dev/packages/flutter_appauth) ，让你的 Flutter 应用用户通过 GoogleOAuth2 认证，并与你的后端连接。不限于 Firebase。

***TL；DR:这个项目的 GitHub repo 可以在这里*** ***找到*** [***。***](https://github.com/florianabel/social-auth-example-flutter)

![](img/816da97f510ee6ebe244686e737041eb.png)

[freestocks](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 介绍

本文是在你的 Flutter 应用程序中使用 GoogleOAuth2 实现社交认证的分步指南，使用 [Flutter AppAuth](https://pub.dev/packages/flutter_appauth) 。成功认证后，您将收到一个访问令牌，以便访问您的后端。我写了另一篇文章解释如何用 Django 应用程序处理后端。然而，你可以自由地使用任何你觉得最舒服的后端。

# 使用 OAuth2 进行身份验证

OAuth2 的身份验证基于不同的流程，这取决于您的应用程序所使用的特定设置。Aaron Parecki 和 Auth0 的两篇文章很好地涵盖了基础知识以及为您的项目选择哪个流程。

## 带有证明密钥的授权代码流(PKCE)

在本例中，我们使用[带有代码交换证明密钥的授权代码流(PKCE)](https://oauth.net/2/pkce/) ，这是公共客户端(如单页面和移动应用程序)向 OAuth2 提供者请求访问令牌时的推荐方式。提供者的身份验证过程发生在客户端。身份验证成功后，客户端将收到的访问令牌交换为来自后端的身份验证令牌。有了这个令牌，客户端就可以使用后端提供的受限端点。

关于这个流程的更多细节在这篇 [Auth0 文章](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow-with-proof-key-for-code-exchange-pkce)中有很好的解释。我们的例外是，我们直接连接到 GoogleOAuth2，而不是 Auth0。

# 向 GoogleOAuth2 注册您的应用程序

为了让使用谷歌的社交登录功能，您的应用程序需要在[谷歌云控制台](https://console.cloud.google.com/)注册。

1.  创建新项目
2.  定义您的 [OAuth 同意屏幕](https://console.cloud.google.com/apis/credentials/consent)
3.  创建[凭证](https://console.cloud.google.com/apis/credentials)

## 创建凭据

*注意:我们需要创建两组凭证，一组用于 iOS，一组用于 Android。*

我们从[全权证书开始——页面](https://console.cloud.google.com/apis/credentials):

1.  选择页面顶部的`+ Create Credentials`
2.  选择`OAuth client ID`
3.  申请类型为`iOS`或`Android`
4.  随你的便吧
5.  填写`Bundle ID` (iOS)或`Package Name` (Android)
6.  创建并填写`SHA-1 certificate fingerprint`(仅适用于 Android)
7.  复制并保存`Client ID`

**Bundle ID 和 Package Name
T24[Bundle ID](https://developer.apple.com/documentation/appstoreconnectapi/bundle_ids)和 [Package Name](https://support.google.com/admob/answer/9972781?hl=en) 是唯一标识符，由您选择。对于苹果应用商店或谷歌 Play 商店中的两个应用程序来说，它们不可能分别是相同的。常见的格式是`com.company.appname`，例如`com.socialauthexample.mobile_app`。**

注意:不要使用连字符(-)或下划线(_)，否则可能会出错。

**SHA-1 证书指纹

要为您的开发环境获取它，您可以遵循以下步骤。查看[这篇文章](https://www.kindacode.com/article/android-how-to-get-sha-certificate-fingerprints/)以获得更多关于为生产获取指纹的信息和说明。**

**对于 Mac:**

```
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

**对于 Windows:**

```
keytool -list -v -keystore C:\Users\<Your User Name>\.android\debug.keystore -alias androiddebugkey
```

*注意:确保之前至少运行一次 Android Studio，否则可能会收到错误。*

# 设置您的应用程序

## 创建基础应用程序

```
flutter create --org com.socialauthexample mobile_app
```

## 安装所需的依赖项

我们的社交认证解决方案和令牌的安全存储需要三个包。

[**flutter _ app auth**](https://pub.dev/packages/flutter_appauth)维护良好的 Flutter 包，包裹在 [AppAuth](https://appauth.io/) 周围，为用户提供认证和授权。

[**http**](https://pub.dev/packages/http)基于未来的库来执行 HTTP 请求，由 Dart 团队开发。

[**flutter _ secure _ storage**](https://pub.dev/packages/flutter_secure_storage)一个将数据存储在安全存储器中的 Flutter 插件。

```
flutter pub add flutter_appauth && flutter pub add http && flutter pub add flutter_secure_storage
```

# 为我们的应用程序提供凭证

为了成功地认证我们的用户，我们需要提供客户端 id(以前获得的)并将 URL 重定向到我们的应用程序。

## 重定向 URL 格式

重定向或回调 URL 是 OAuth2 服务器在成功认证后将用户发送到的地址。谷歌在格式上有[具体要求](https://developers.google.com/identity/protocols/oauth2/native-app#request-parameter-redirect_uri)。我们在本例中使用的格式基于您的客户端 id:`com.googleusercontent.apps.<CLIENT-ID>:redirect_uri_path`

**iOS**

```
com.googleusercontent.apps.<IOS-CLIENT-ID>:/oauthredirect
```

**安卓**

```
com.googleusercontent.apps.<ANDROID-CLIENT-ID>:/oauthredirect
```

**使用代码库中的变量** 我们使用一个助手文件`lib/helper/constants.dart`来存储应用程序的所有常量，并根据应用程序运行的平台传递正确的凭证。

社交验证示例应用程序的常数

> 注意:出于安全原因，凭证不应该成为源代码的一部分。提供它们最安全的方式是使用环境变量，例如 [flutter_config](https://pub.dev/packages/flutter_config) ，但是这超出了本文的范围。

## 设置 URL 方案

除了为 Flutter AppAuth 提供重定向 URL 之外，我们还需要让我们的应用程序知道所需的 URL 方案。

**CFBundleURLSchemes(iOS)** 为了配置 iOS 的 URL 方案，我们在`/ios/Runner/Info.plist`文件中添加了一个新的`dict`标签`CFBundleURLTypes`。

**appAuthRedirectScheme(Android)** 对于 Android，我们需要调整`/android/app/build.gradle`文件中的以下条目。

*注意:确保 URL 方案和重定向 URL 匹配，否则您可能无法在认证后被重定向。*

# 构建应用程序

我们的应用程序将为未经认证的用户显示一个登录屏幕，在成功认证后，该屏幕将被我们的主屏幕所取代。此外，以前登录的用户在重新启动应用程序后仍保持登录状态。

我们正在用我们的`lib/main.dart`文件中的一些基本逻辑创建一个用户界面，三个额外的屏幕，以及一个处理认证的服务`lib/services/authentication_service.dart`。

## 应用界面

main.dart
我们的`main.dart`持有我们的应用程序的基础结构。两个标志，`isLoading`和`isLoggedIn`，表示当前的认证状态，以及当前是否加载了某个动作。它们的状态由三种方法处理(`setLoadingState`、`setAuthenticatedState`和`setUnauthenticatedState`)，构建器相应地提供正确的屏幕、`LoadingScreen`、`MainScreen`、`AuthenticationScreen`。

在应用程序初始化时，我们的应用程序会检查用户当前是否已经过身份验证，并相应地设置状态(关于如何发生的细节将在下面进一步讨论)。如果用户已经通过身份验证，则提供`MainScreen`，否则，我们的用户将看到`AuthenticationScreen`。此外，我们将适当的函数作为回调来传递，以便稍后更改状态。

**loading_screen.dart** 

**authentic ation _ screen . dart
成功时，我们的应用程序切换到认证状态，显示`MainScreen`，否则，它返回到未认证状态，再次显示`AuthenticationScreen`。**

**main_screen.dart
当单击按钮时，我们的 logout 方法被调用，该方法又调用身份验证服务，并在成功时将应用程序的状态更改为 unauthenticated。**

## 认证服务

真正的魔力在于我们的认证服务。它检查我们安全存储中已经存在的刷新令牌，执行身份验证和刷新过程，并保存和删除我们获得的令牌。

**基础知识** 身份验证服务是作为单例构建的，这意味着任何时候都只能存在一个实例。构造函数是私有的，只能从类内部调用。为了访问服务，我们总是需要调用实例`AuthService.instance.someMethod()`。该服务提供三个公共方法和一个私有方法供内部使用。

**初始化** 第一个方法，在`main.dart`中被应用程序调用的是`initAuth()`。它检查现有的刷新令牌，并试图用它获得新的访问令牌。如果成功了，我们就有了一个登录的用户，我们的应用程序就进入了认证状态。如果没有，要么是没有用户登录，要么是令牌过期。在这两种情况下，我们的应用程序都会进入未经验证的状态。

**登录** 一旦用户点击登录按钮，我们的`login()`方法就会被调用并开始认证过程。出现一个 Google 登录窗口，用户可以进行身份验证。如果一切顺利，将返回一个带有访问和刷新令牌的结果。

注销后，保存的刷新令牌被删除，我们的应用程序切换回未认证状态。

**处理认证结果** `_handleAuthResult`是我们服务中唯一的私有方法。它负责处理我们在验证时或在现有刷新令牌的帮助下试图获得新的访问令牌时从 Google 得到的响应。
如果结果有效，我们更新刷新令牌。之后，我们将获得的访问令牌与来自我们自己后端的身份验证令牌进行交换，并保存它以供将来使用。

> 注意:由于这超出了本文的范围，我在方法的未注释部分为您展示了一个简单的例子。您可以根据需要用自己的后端来实现它。对于这个场景，我们只是假设后端返回了一个我们可以使用的有效令牌。

# 摘要

现在，您有了使用 Google 登录的基本社交认证模式。通过使用不同的凭证，它可以很容易地改变为不同的提供商，如脸书或 Twitter。之后，您的后端将处理获得的访问令牌，并为您提供一个新的身份验证令牌。关于 Django 后端的例子，请查看我的另一篇文章。

**随意在评论里伸手或者关注我**[**Twitter**](https://twitter.com/florian_abel_)**(**[**@ Florian _ Abel _**](https://twitter.com/florian_abel_)**)。我很高兴听到你的想法、问题和经历。**