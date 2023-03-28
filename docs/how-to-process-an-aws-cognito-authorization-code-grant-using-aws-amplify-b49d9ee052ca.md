# 如何使用 AWS Amplify 处理 AWS 认知授权码授权

> 原文：<https://medium.com/codex/how-to-process-an-aws-cognito-authorization-code-grant-using-aws-amplify-b49d9ee052ca?source=collection_archive---------2----------------------->

![](img/63860747d6b26162a1290398ff45b0bd.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

大多数使用 AWS Cognito + Amplify 的开发人员都利用 Amplify 内的内置`urlListener`，它会自动处理包含授权代码授权的 Cognito web 响应。然而，如果您正在构建一个移动 JavaScript 应用程序，并且想要提供社交登录，如果深层链接不能用于处理您的重定向，您可能需要自己处理代码授权。我们正在对 Corkhounds.com 的移动应用程序进行一次大检修，我们最近就面临这种情况。如何以编程方式处理授权代码授权并不直观，需要在网上进行大量挖掘才能弄清楚。如果你好奇这是如何工作的，这篇文章就是为你准备的。

# 假设和概述

我们假设您已经在使用 AWS Cognito 和 Amplify，并且您已经有一个 Cognito 用户池。启动并运行 Cognito 和 Amplify 本身就是一个主题。我们还假设您正在使用授权码授权响应类型，并且您成功地将 Amplify 的`Auth.federatedSignIn()`方法与脸书、谷歌或苹果社交登录或托管 UI 一起使用。您应该会收到一个带有授权码授权的 Cognito web 响应，类似于:

`[https://YOUR_APP_URL/redirect_uri?code=AUTHORIZATION_CODE&state=STATE](https://YOUR_APP_URL/redirect_uri?code=AUTHORIZATION_CODE&state=STATE)`

首先，我们将向您展示如何使用 Amplify 身份验证模块解析这个 web 响应，然后逐步介绍创建认知用户会话的一种方法，最后介绍如何让 Amplify 获取/识别该会话，并显示该用户当前已通过身份验证。

# 处理代码授权

当您在网上搜索解析 Cognito web 响应的方法时，您无疑会找到大量关于 amazon-cognito-auth-js 包和`parseCognitoWebResponse()`方法的参考资料。但是，原来的 amazon-cognito-auth-js 包已经被 Amazon[归档](https://github.com/amazon-archives/amazon-cognito-auth-js)，Amplify 的认证模块接管了。在 Amplify 代码库中没有`parseCognitoWebResponse()`，所以看起来这个特性不再被支持了。

幸运的是，有一个处理 Cognito web 响应的示例，该响应包含授权代码 grant 以检索访问、id 和刷新令牌。Amplify 的`Auth._oAuthHandler.handleAuthResponse()`根据 oauth2/token 端点解析并提交代码授权，以检索令牌。你可以[查看 Amplify 的 github repo 中的代码](https://github.com/aws-amplify/amplify-js/blob/6de9a1d743deef8de5205590bf7cf8134a5fb5f4/packages/auth/src/OAuth/OAuth.ts#L217)，看看它是如何工作的。不幸的是，`_oAuthHandler`函数在 TypeScript 中被标记为`private`。如果您没有使用 TypeScript，您可以使用这个函数，但是这是很危险的，因为开发团队可能会在未来的版本中进行修改。我已经[提交了一个功能请求](https://github.com/aws-amplify/amplify-js/issues/8933)来公开这个功能，但是到目前为止还没有得到回复。

我将向你展示它是如何工作的，但是继续下去你要自担风险。首先，我们需要安装 amazon-cognito-identity-js 包及其依赖项 aws-sdk:

`npm install aws-sdk amazon-cognito-identity-js`

让我们浏览下面的代码示例。我在整个示例中嵌套了注释，以解释我们在每个步骤中所做的事情。您应该已经导入了 Amplify 和 Auth 模块。为了简单起见，我们将从 amazon-cognito-identity-js 包中导入所有模块。因为您已经配置了 Amplify 和 Auth(基于我们上面的假设)，我们现在可以使用`Auth._oAuthHandler.handleAuthResponse()`方法来处理 cognito web 响应 url。然后，我们将解析响应以创建 CognitoAccessToken、CognitoIdToken 和 CognitoRefreshToken。使用这些标记，我们可以创建一个 CognitoUserSession。

此时，我们有一个 Cognito 用户会话，但是它还没有被 Amplify 识别。如果您现在运行`Auth.currentAuthenticatedUser()`方法，您会发现没有经过身份验证的用户。出现这种情况的主要原因似乎是因为会话/令牌没有存储在 Amplify 可以获取它们的地方。你可以在 Amplify 的 GitHub 问题 [#6555](https://github.com/aws-amplify/amplify-js/issues/6555) 中了解更多信息。

# 创建 Amplify 认证用户会话

现在我们有了一个 Cognito 用户会话，我们需要将它告诉 Amplify。我发现的最简单的方法记录在亚马逊 Cognito 的[网站文档](https://docs.aws.amazon.com/cognito/latest/developerguide/authentication.html)中，我将在下面的代码示例中分解它。像以前一样，我在这个示例中嵌套了注释来解释我们在每个步骤中做了什么。

本质上，这些步骤是创建一个 CognitoUserPool 对象，然后我们可以使用它和 accessToken 中包含的用户的`username`来实例化一个 CognitoUser。有了 CognitoUser 对象，我们可以使用之前创建的 CognitoUserSession 调用`setSignInUserSession()`方法来完成创建 Amplify 用户会话。

现在，Amplify 将正确返回经过身份验证的用户。如果你像我一样，你会认为应该有一个更简单的方法来实现这个结果——假设 Amplify/Auth 已经配置好了，并且你有单独的 CognitoUserSession 而且可能有。但是我还没找到。

# 把所有的放在一起

为了简单起见，现在我们将把所有代码缝合在一起。

# 结论

希望您发现这篇博客对于将授权码授权转变为经过验证的用户很有用。如有任何问题，请随时联系我们。

*原载于 2021 年 7 月 30 日 https://blog.corkhounds.com*[](https://blog.corkhounds.com/2021/07/30/authenticating/)**。**