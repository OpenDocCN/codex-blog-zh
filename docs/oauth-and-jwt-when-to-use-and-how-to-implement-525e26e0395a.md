# OAuth 和 JWT —何时使用以及如何实施

> 原文：<https://medium.com/codex/oauth-and-jwt-when-to-use-and-how-to-implement-525e26e0395a?source=collection_archive---------4----------------------->

![](img/8ba37c7a150dab7572e29e5211e9d7cd.png)

授权，认证…谁知道区别，对不对？

在深入 OAuth 和 JWT 的概念之前，您可以快速回顾一下，了解授权和身份验证之间的区别，

在计算机安全领域，认证和授权是两个相关但不同的概念。

**认证**是验证用户或系统身份的过程。这通常需要用户提供用户名和密码，然后系统根据注册用户的数据库进行验证。如果用户名和密码与数据库中的条目匹配，则认为用户通过了身份验证，并被授予访问系统的权限。

**另一方面，授权**是确定经过身份验证的用户是否有权执行特定操作或访问特定资源的过程。这通常涉及系统对照一组规则或策略检查经过身份验证的用户的访问权限或特权，以确定用户是否被授权执行所请求的操作。

总之，认证是验证用户身份的过程，而授权是验证用户权限的过程。这两个过程共同构成了计算机系统中安全访问控制的基础。

# OAuth 是什么？

OAuth(开放授权)是一个**开放的访问授权标准，允许用户授权第三方应用程序访问他们的资源，而无需共享他们的密码**。它为应用程序提供了一种**安全的方式**来代表用户**访问资源，而不需要用户共享他们的凭证**。

OAuth 使用令牌和秘密的组合来认证用户和授权对资源的访问。它允许用户**通过提供一个令牌来授权对他们的资源的访问，这个令牌由 OAuth 服务器发出，并与访问请求一起传递**。OAuth 服务器随后验证令牌，如果令牌有效，则**授予对所请求资源的访问权**。

# 什么是 JWT？

**JWT (JSON Web Token)** 是一种简洁的、URL 安全的方式来表示要在双方之间传输的声明。通常**用于授权请求**和**在各方**之间交换信息，例如**在服务器和客户端**应用之间。

JWT 是一个**自包含的令牌**，这意味着它在令牌本身内包含了**关于用户的所有必要信息。这种**消除了服务器查询数据库来验证用户凭证的需要**。相反，服务器可以简单地解码 JWT 并提取用户的相关信息，比如他们的用户名、角色和其他声明。**

JWT**基于 JSON(JavaScript Object Notation)**数据格式，广泛用于应用程序之间的数据交换。它以一种紧凑和 URL 安全的方式进行编码，这使得各方之间的传输和交换变得容易。它还经过数字签名，这确保了它不能被篡改或伪造。

JWT 通常与其他协议和技术(如 OAuth 和 OpenID Connect)结合使用，以提供完整的身份验证和授权解决方案。**它被许多流行的网络和移动应用程序广泛使用，如脸书、谷歌和 Twitter** ，用于安全地交换认证用户的信息。

# 核心差异

OAuth 和 JWT 是处理认证和授权的两种不同的标准。OAuth(开放授权)是一种开放的访问授权标准，它允许用户授权第三方应用程序访问他们的资源，而无需共享他们的密码。它的工作原理是为应用程序提供一种安全的方式来代表用户访问资源，而不需要用户共享他们的凭据。

# Node.js 中的 OAuth 实现

要在 Node.js 应用程序中实现 OAuth，可以使用第三方库，如 Passport.js. Passport.js 是 Node.js 的一个流行的身份验证中间件，它提供了一种简单灵活的方法来实现 OAuth。

要使用 **Passport.js** ，首先需要将其安装在 Node.js 应用程序中。您可以使用以下命令来完成此操作:

```
npm install passport
```

接下来，您需要配置 **Passport.js** 来使用 **OAuth** 认证策略。这包括指定 OAuth 提供者(如脸书或 T21)并提供必要的凭证，如客户端 ID 和密码。

一旦配置了 Passport.js，就可以用它来处理身份验证过程。这通常包括设置处理身份验证请求、回调和其他事件的路由。Passport.js 将负责 OAuth 认证流程的细节，比如生成和验证令牌，以及向您的应用程序提供经过认证的用户的信息。

下面，您可以找到一个 Node.js 应用程序的示例，该应用程序使用 **Passport.js** 通过脸书实现 OAuth:

```
const express = require('express');
const passport = require('passport');
const FacebookStrategy = require('passport-facebook').Strategy;
const app = express();
// Configure Passport to use the Facebook strategy
passport.use(new FacebookStrategy({
  clientID: 'YOUR_CLIENT_ID',
  clientSecret: 'YOUR_CLIENT_SECRET',
  callbackURL: 'http://localhost:3000/auth/facebook/callback'
}, (accessToken, refreshToken, profile, done) => {
  // Save the authenticated user's information
  done(null, profile);
}));
// Set up routes to handle the authentication flow
app.get('/auth/facebook', passport.authenticate('facebook'));
app.get('/auth/facebook/callback',
  passport.authenticate('facebook', { failureRedirect: '/login' }),
  (req, res) => {
    // Successful authentication, redirect to the user's profile page
    res.redirect('/profile');
  }
);
app.listen(3000);
```

# Node.js 中的 JWT 实现

JWT(JSON Web Token)是一种在双方之间安全传输信息的方式。在 Node.js 应用程序中，可以使用第三方库(如 jsonwebtoken)轻松生成和验证 JWT 令牌。

要生成 JWT 令牌，**首先需要用想要传输的信息创建一个 JSON 对象，比如认证用户的用户名和角色**。然后，您可以使用 jsonwebtoken 库来**使用秘密密钥**对这个 JSON 对象进行签名。这将**生成一个 JWT 令牌，该令牌可以传输到客户端**。

要验证 JWT 令牌，可以使用 jsonwebtoken 库的 **verify** 方法。**该方法将令牌和密钥作为参数，如果令牌有效，则返回解码后的 JSON 对象**。**这允许服务器根据 JWT 中包含的信息对用户进行身份验证并授权对受保护资源的访问。**

总的来说， **JWT 提供了一种简单而安全的方式在各方之间传输信息**，jsonwebtoken 使得在 Node.js 应用程序中实现起来很容易。

要在 Node.js 应用程序中使用 JSON Web 令牌(jwt ),您必须安装`jsonwebtoken`包。

```
npm install jsonwebtoken
```

一旦安装了这个包，您就可以在 Node.js 代码中使用它来**创建和验证 jwt**。下面是一个示例，说明如何使用`jsonwebtoken`包来创建一个 JWT，断言一组关于与令牌相关联的用户身份的特定声明:

```
const jwt = require('jsonwebtoken');
// Set the secret that will be used to sign the JWT
const jwtSecret = '<your-jwt-secret>';
// Set the claims that the JWT will assert about the user
const claims = {
  userId: 12345,
  isAdmin: true
};
// Use the JWT library to create a JWT using the claims and the secret
const token = jwt.sign(claims, jwtSecret);
```

一旦您创建了一个 JWT，**您就可以在您的 API** 中使用它来验证用户。这就是你如何使用`jsonwebtoken`包来**验证一个 JWT 并提取其中包含的声明**:

```
const jwt = require('jsonwebtoken');
// Set the secret that will be used to verify the JWT
const jwtSecret = '<your-jwt-secret>';
app.get('/', (req, res) => {
  // Get the JWT from the request headers
  const token = req.headers.authorization;
  // Use the JWT library to verify the JWT and extract the claims
  const decoded = jwt.verify(token, jwtSecret);
  // Use the claims to authenticate the user and provide access to the API
  if (decoded.isAdmin) {
    res.send('Access granted to admin user');
  } else {
    res.send('Access granted to regular user');
  }
});
```

# 对 JWT 使用 OAuth

下面是一个示例，说明如何使用这些库来创建一个使用 OAuth 和 JWT 进行身份验证的简单服务器:

```
const express = require('express');
const oauth = require('oauth');
const jwt = require('jsonwebtoken');
const app = express();
// Create an OAuth client to make requests to the OAuth provider
const oauthClient = new oauth.OAuth(
  'https://<OAuth-provider-URL>/request_token',
  'https://<OAuth-provider-URL>/access_token',
  '<consumer-key>',
  '<consumer-secret>',
  '1.0',
  '<callback-URL>',
  'HMAC-SHA1'
);
// Use the OAuth client to make a request to the OAuth provider to get an access token
oauthClient.getOAuthAccessToken(
  '<request-token>',
  '<request-token-secret>',
  (error, accessToken, accessTokenSecret, results) => {
    if (error) {
      // Handle error
    } else {
      // Use the access token to make authenticated requests
      const options = {
        url: 'https://<API-URL>/protected_resource',
        oauth: {
          consumer_key: '<consumer-key>',
          consumer_secret: '<consumer-secret>',
          token: accessToken,
          token_secret: accessTokenSecret
        }
      };
      request.get(options, (error, response, body) => {
        if (error) {
          // Handle error
        } else {
          // Use the JWT library to create a JWT using the access token
          const token = jwt.sign({ accessToken }, '<jwt-secret>');
          // Use the JWT in your API calls to authenticate the user
          app.get('/', (req, res) => {
            const decoded = jwt.verify(req.headers.authorization, '<jwt-secret>');
            res.send(decoded);
          });
        }
      });
    }
  }
);
```

这是如何在 Node.js 应用程序中使用 OAuth 和 JWT 的一个非常基本的例子。在现实世界的应用程序中，您需要**处理错误情况，实现适当的安全措施，并提供一个用户友好的认证界面**。

# 结论

总之， **JSON Web 令牌(jwt)**和 **OAuth** 都是用于**认证和授权**的行业标准协议。jwt 用于创建访问令牌，这些令牌断言与令牌相关联的用户身份的一组特定声明。OAuth 是一种开放的访问授权标准，它允许第三方服务授予对 HTTP 服务的有限访问权限。

**jwt**和 **OAuth** 可以一起使用，在 Node.js 应用程序中实现安全认证和授权。jwt 可以用来创建访问令牌，这些令牌用一个秘密进行签名，并且可以由 API 进行验证，以对用户进行身份验证。OAuth 可用于代表用户授予第三方服务对 API 的访问权限，而无需用户共享他们的凭证。

总的来说，JWTs 和 OAuth **是实现安全认证和授权**的强大工具。