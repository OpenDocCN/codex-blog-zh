# 使用 python-social-auth 和 Django REST 框架的 Google 登录 REST API

> 原文：<https://medium.com/codex/google-sign-in-rest-api-with-python-social-auth-and-django-rest-framework-4d087cd6d47f?source=collection_archive---------0----------------------->

为在你的前端或移动应用程序中注册了谷歌、脸书或其他第三方 OAuth 提供商的用户验证你的 Django 后端。

***TL；DR:这个项目的 GitHub repo 可以在这里找到***[](https://github.com/florianabel/social-auth-example-django)****。****

*![](img/4d0cb67b61b1982f139f6a6e7990c87f.png)*

*[Ed Hardie](https://unsplash.com/@impelling?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片*

# *介绍*

*在本文中，您将为社交认证构建 REST API 端点。它们可以和一个单独的前端一起使用，就像一个单页或移动应用程序。我们将使用 [python-social-auth](https://python-social-auth.readthedocs.io/) 和[Django REST Framework](https://www.django-rest-framework.org/)(DRF)来交换由第三方 OAuth 提供者(GoogleOAuth2)获得的访问令牌和来自 DRF 的认证令牌。*

**注意:在这个例子中，我们将只使用 API 端点，不使用 Djangos 的内置模板语言。**

## *认证过程*

*OAuth2 身份验证提供了不同的流程，这取决于您的应用程序所使用的特定设置。Aaron Parecki 和 Auth0 的两篇文章很好地涵盖了基础知识以及为您的项目选择哪个流程。*

*在本例中，我们使用[带有代码交换证明密钥的授权代码流(PKCE)](https://oauth.net/2/pkce/) ，这是公共客户端(如单页面和移动应用程序)向 OAuth2 提供者请求访问令牌时的推荐方式。与提供商的身份验证过程发生在客户端，在成功的身份验证之后，客户端将收到的访问令牌与来自后端的另一个身份验证令牌进行交换。有了这个令牌，客户机就可以通过身份验证，使用后端提供的 API。*

*关于这个流程的更多细节在这篇 [Auth0 文章](https://auth0.com/docs/get-started/authentication-and-authorization-flow/authorization-code-flow-with-proof-key-for-code-exchange-pkce)中有很好的解释。我们的例外是，我们直接连接到 GoogleOAuth2，而不是 Auth0。*

****这是适合我的社交认证包吗？*** *注意:如果您不确定这是否是您的用例的最佳包，您可以在* [*这篇文章*](/codex/django-allauth-vs-dj-rest-auth-vs-python-social-auth-vs-drf-social-oauth2-ef7d50f92d16) *中找到 Django 的四个流行的社会认证包的简要比较。**

# *先决条件*

## *姜戈*

*在我们开始之前，我要求你安装和设置你的 Django 项目。如果您不确定如何操作，请查看'[Django](https://www.djangoproject.com/start/)入门'文档。*

## *Django REST 框架*

*我们正在使用[Django REST Framework](https://www.django-rest-framework.org/)(DRF)来设置 REST API 端点，建议对它有一个基本的了解。这篇文章的范围不会涵盖太多关于 DRF 本身的内容，但是，在它的“[快速入门](https://www.django-rest-framework.org/tutorial/quickstart/#quickstart)”教程中提供的信息足够让你在这里继续学习下去了。*

## *获取访问令牌*

*为了测试您的应用程序，您将需要一个 GoogleOAuth2 访问令牌。这通常由您的前端获得，如单页或移动应用程序。出于开发目的，您也可以使用 web 浏览器和 curl 来完成，如这里的[所述](/codex/manually-obtain-googleoauth2-access-token-with-your-web-browser-and-curl-fd93effe15ff)。*

**注意:当向客户端发送 OAuth 请求时，确保请求所需的范围，以充分填充您的用户模型。
对于 GoogleOAuth2 来说就是:* `*scope = ['email', 'profile']*` *。**

# *入门指南*

*假设您已经建立了您的 Django 项目，并且已经应用了您的初始迁移，让我们从添加一个名为`users`的新应用程序开始。在那里，我们正在构建我们的身份验证逻辑:*

```
*python manage.py startapp users*
```

*接下来，我们安装所需的第三方软件包。*

```
*pip install django \djangorestframework \social-auth-core \social-auth-app-django*
```

*安装后，我们在设置中注册用户和外部应用程序。py:*

*最后，我们运行 [DRF](https://www.django-rest-framework.org/) 和 [python-social-auth](https://python-social-auth.readthedocs.io/) 带来的迁移。*

```
*python manage.py migrate*
```

****对于 PostgreSQL 用户:*** *在这个例子中，我们使用的是 SQLite3 数据库，Django 默认使用这个数据库。如果您正在使用 PostgreSQL 数据库，建议使用内置的 JSONB 字段来存储提取的* `*extra_data*` *，方法是将这一行添加到您的 settings.py:* `SOCIAL_AUTH_JSONFIELD_ENABLED = True`*

****针对 MongoEngineORM 用户:*** *如果你使用的是*[*MongoEngine*](http://mongoengine.org/)*ORM，安装设置略有不同。请查看*[*python-social-auth*](https://python-social-auth.readthedocs.io/en/latest/configuration/django.html)*的官方文档。**

# *配置*

## *来自 DRF 的基于令牌的身份验证和权限类*

*对于 DRF，我们正在我们的`settings.py`中添加一些条目来定义认证和许可类。*

*DRF 的设置*

***认证** 通过将`rest_framework.authtoken`添加到我们的`INSTALLED_APPS`并将`TokenAuthentication`添加到默认认证类列表，我们启用了 [DRFs 内置令牌认证](https://www.django-rest-framework.org/api-guide/authentication/#tokenauthentication)。它提供了一个简单的基于令牌的 HTTP 认证方案。*

*在我们的示例中，我们将从前端的社交提供者那里接收到的访问令牌交换为这些 toke 中的一个。然后可以使用它来验证我们受保护的 API 端点。*

****注意:这不是一个非常安全的认证方案，仅用于演示目的。对于生产级应用程序，您需要考虑更安全的替代方案。****

***权限** 默认情况下，所有 API 请求都需要通过该设置进行认证。稍后，我们可以在打算公开的 API 上改变这种行为。*

## *身份验证后端*

*Django 保存了一个认证后端列表，用于遍历一个认证请求。它默认为 Django 的基本认证后端，但可以根据需要[扩展或替换](https://docs.djangoproject.com/en/4.0/topics/auth/customizing/#authentication-backends)。在这个例子中，我们为 [GoogleOAuth2](https://python-social-auth.readthedocs.io/en/latest/backends/google.html#google-oauth2) 认证添加了一个后端。你可以在这里找到 python-social-auth 可用选项的完整列表。*

**注意:如果* `*AUTHENTICATION_BACKENDS*` *条目不存在，必须自己创建。**

## *社交授权管道*

*[管道机制](https://python-social-auth.readthedocs.io/en/latest/pipeline.html)用于处理 python-social-auth 中的认证、关联和断开流程。这些管道存在默认流，可以根据需要进行调整(添加自定义函数；移除默认项目)。*

*可以找到默认的认证管道，[这里](https://python-social-auth.readthedocs.io/en/latest/pipeline.html#authentication-pipeline)。要定义自己的，可以添加到自己的`settings.py`。*

**注意:在这个例子中，我们使用默认管道，没有添加我们自己的版本。但是，您可以在下面找到一个示例版本。**

## *创建 API 端点*

*我们将使用一个注册端点来注册/登录一个具有来自社交提供者(GoogleOAuth2)的访问令牌的用户。这个终点将对公众开放。此外，我们正在定义第二个端点，该端点可以与后端分发的身份验证令牌一起使用。*

## *创建视图*

## *@psa()装饰器*

*`@psa()`装饰是我们的观点的关键部分。它将我们的视图与 [python-social-auth](https://python-social-auth.readthedocs.io/) 连接起来，并向请求对象添加细节。*

*   *社会战略(在我们的例子中，Django)*
*   *要使用的社交认证后端(在我们的例子中是 GoogleOAuth2)，基于我们的观点中的`backend`参数。*
*   *提供重定向 URL(在我们的例子中:无。我们在客户端执行认证)*

**注:遗憾的是，@psa() decorator 在*[*python-social-auth*](https://python-social-auth.readthedocs.io/en/latest/use_cases.html?highlight=%40psa#signup-by-oauth-access-token)*的官方文档中几乎没有提及，但在这个*[*stack overflow post*](https://stackoverflow.com/questions/28626718/what-is-the-purpose-of-the-psa-decorator-and-how-do-we-use-it)*中可以找到一些附加信息。**

## *添加 URL*

# *测试我们的 API*

## *注册/登录用户*

*使用从我们的客户端获得的访问令牌，我们现在可以测试我们的 API:*

```
*curl -X POST \http://127.0.0.1/api/register-by-access-token/social/google-oauth2/ \-H 'Accept: application/json' \-H 'Content-Type: application/json' \-d '{"access_token": "<GoogleOAuth2-ACCESS-TOKEN-FROM-CLIENT>"}'*
```

*这会产生类似于`{"token": "<TOKEN-FROM-BACKEND"}`的结果。*

## *测试返回的令牌*

*使用从后端接收到的身份验证令牌，我们现在可以访问受保护的端点。*

```
*curl \http://127.0.0.1/api/authentication-test/ \-H 'Accept: application/json' \-H 'Content-Type: application/json' \-H 'Authorization: Token fd4d4cca9163e5afb251f6310b9c725413979f10'*
```

*`{"message": "User successfully authenticated"}`*

# *摘要*

*现在您有了一个简单的 REST API 端点来创建和登录 Google 用户。要接收访问，只需将从前端获得的访问令牌交换为后端的身份验证令牌。要使用其他认证提供商，如脸书或 Twitter，遵循相同的原则，使用不同的认证后端。*

***欢迎在评论里发表你的问题/评论或者在** [**推特**](https://twitter.com/florian_abel_)**(**[**@ Florian _ Abel _**](https://twitter.com/florian_abel_)**)上打我。我很高兴听到你的想法、问题和经历。***