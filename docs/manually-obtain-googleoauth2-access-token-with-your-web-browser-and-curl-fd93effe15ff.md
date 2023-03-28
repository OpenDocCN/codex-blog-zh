# 使用 web 浏览器和 curl 手动获取 GoogleOAuth2 访问令牌

> 原文：<https://medium.com/codex/manually-obtain-googleoauth2-access-token-with-your-web-browser-and-curl-fd93effe15ff?source=collection_archive---------3----------------------->

如果出于开发(或其他)原因，您需要手动从 GoogleOAuth2 获取访问令牌，您可以使用 web 浏览器和 curl 通过几个简单的步骤来完成。

![](img/d1f860b15619820808cfa390d54a79e1.png)

照片由[孙富](https://unsplash.com/@zisun_word?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

*注意:本文部分基于以下两个资源。过去曾有过* [*另一种方式做这个*](https://www.daimto.com/how-to-get-a-google-access-token-with-curl/) *，最近停止工作了。在我寻找新的解决方案的过程中，我在 StackOverflow* *上找到了这个* [*的帖子，里面正在讨论这个方法。*](https://stackoverflow.com/questions/66940072/get-google-oauth2-access-token-using-only-curl)

# 向 GoogleOAuth2 注册应用程序

为了从 GoogleOAuth2 获得一个访问令牌，你首先需要在 [Google Cloud 控制台](https://console.cloud.google.com/)注册你的应用。

1.  创建新项目
2.  定义您的 [OAuth 同意屏幕](https://console.cloud.google.com/apis/credentials/consent)
3.  创建[凭证](https://console.cloud.google.com/apis/credentials)

## 创建凭据

我们从第[页的](https://console.cloud.google.com/apis/credentials)开始:

1.  选择页面顶部的`+ Create Credentials`
2.  选择`OAuth client ID`
3.  `Web application`为申请类型
4.  随你的便吧
5.  在`Authorized redirect URIs`下选择`+ Add URI`并添加`[http://127.0.0.1](http://127.0.0.1)`
6.  选择`Create`
7.  复制并保存显示的`Client ID`和`Client Secret`

# 获取访问令牌

## 用户认证

在您的 web 浏览器中，打开以下 URI(不要忘记填写您的客户端 id)并验证您的应用程序(如果需要，请登录您的帐户)。

```
https://accounts.google.com/o/oauth2/auth?client_id=<CLIENT-ID>&redirect_uri=http://127.0.0.1&scope=profile&email&response_type=code&include_granted_scopes=true&access_type=offline&state=state_parameter_passthrough_value
```

认证后，Google 将您重定向到您指定的重定向 URI，它负责处理认证请求响应。由于没有服务器在此 URI 上运行，您的浏览器会显示一个错误页面。但是，在浏览器的地址栏中，你会发现来自谷歌的重定向请求。

```
http://127.0.0.1/?...&code=<CODE>&...
```

在您的内部有一个名为`code`的参数，我们将为下一步复制并保存它。

## 请求访问令牌

利用之前获得的代码参数，我们现在运行一个 curl 请求，向 Google 请求访问令牌。填写您的`code`、`client id`、`client secret`和`redirect URI`，并在您的终端中运行该命令。

```
curl -X POST https://oauth2.googleapis.com/token \
-d "code=<CODE>&client_id=<CLIENT-ID>&client_secret=<CLIENT-SECRET>&redirect_uri=<REDIRECT-URI>&access_type=offline&grant_type=authorization_code"
```

这会给你一个这样的回答:

```
{ "access_token": "<ACCESS-TOKEN>", "expires_in": 3599, "refresh_token": "<REFRESH-TOKEN>", "scope": "https://www.googleapis.com/auth/user.birthday.read     https://www.googleapis.com/auth/userinfo.email  https://www.googleapis.com/auth/userinfo.profile openid", "token_type": "Bearer", "id_token": "XXX"}
```

现在您有了一个访问令牌，您可以将它用于开发等目的。

## 刷新访问令牌

如果您的访问令牌过期，请使用以下命令刷新它:

```
curl -X POST https://oauth2.googleapis.com/token \
-d "access_type=offline&refresh_token=[ZZZZ]&client_id=<CLIENT-ID>&client_secret=<CLIENT-SECRET>&grant_type=refresh_token"
```

## 其他范围

在本例中，我们请求了用户的电子邮件和个人资料信息。如果您想使用您的访问令牌获取其他作用域，请将所需的作用域添加到您的请求中。

# 结论

通过这些简单的步骤，您可以快速从 GoogleOAuth2 获得一个访问令牌。然后，您可以在开发过程中使用它来测试(部分)您的软件。