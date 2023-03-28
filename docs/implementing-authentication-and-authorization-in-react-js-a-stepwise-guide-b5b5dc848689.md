# 在 React JS 中实现身份验证和授权:分步指南

> 原文：<https://medium.com/codex/implementing-authentication-and-authorization-in-react-js-a-stepwise-guide-b5b5dc848689?source=collection_archive---------9----------------------->

![](img/8497aa04691e6962bbce30c34656c034.png)

授权是应用程序中使用的一个过程，有助于控制信息访问和限制用户执行的操作。类似地，身份验证是检查用户是否被允许访问信息或执行任何操作的过程。如果经过身份验证的用户正在使用您的应用程序，他们已经通过多种方式识别了自己的真实身份，例如提供正确的凭据。

虽然身份验证将帮助您了解用户的真实身份，但它不能帮助您控制访问，这就是为什么我们在身份验证中使用授权。如果没有这两者中的任何一个，你的应用程序就会出现混乱。例如，任何用户都可以更改另一个用户的密码或删除另一个用户发布的数据。

# 我们可以通过以下方式在 ReactJS 中实现身份验证:

*   基于 Cookie 的身份验证
*   基于令牌的认证
*   第三方访问(OAuth、API 令牌)
*   OpenId
*   安全声明标记语言（Security Assertion Markup Language 的缩写）

> 在本文中，我们将在 ReactJS 中使用基于令牌的认证或 JWT 令牌进行认证和授权。请继续阅读，了解其工作原理。

# 更多关于 JWT 令牌

在许多资源中，我们需要限制访问，以确保没有用户能够阻碍其他用户的活动。因此，我们利用 id 和密码进行安全访问和身份验证。然而，这里的问题是 HTTP API 不知道两个不同的请求是否由一个用户提出，因为 HTTP 是无状态的。

虽然我们可以要求用户在每次 API 调用时提供 ID 和密码，但这也意味着糟糕的用户体验。相反，我们需要的是请求一次身份验证并在后续请求中通过其他方式识别用户的能力。一个有效的系统是 JSON Web Token，通常被称为 JWT。

> JWT 令牌是一种开放标准( [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519) )，它提供了一种独立的方法来以安全的方式向不同的方传输信息。因为这些信息是数字签名的，所以是可信的和可验证的。

每当用户登录时，都会生成 JWT 令牌。这对于与一个用户相关联的一个帐户是可访问的。用户可以使用该令牌来利用他们的访问权限，直到他们注销。

# JWT 令牌的结构

通常，您会发现 HTTP 请求头中的 JWT 是使用**承载**模式的**授权**密钥。

使用 JWT 令牌，您可以找到用句点分隔的三个字符串。这些 base 64 编码的字符串是头、有效负载和签名。它看起来像 XXXXXX . yyyyyy . zzzz

**下面我们解码了这些字符串，以便更清楚地理解 JWT 的结构。**

# 页眉

周期之前的第一部分是报头，其包含令牌或 JWT 的元信息或数据。它包含两个部分:令牌类型和签名算法，该算法以 Base64 编码，构成令牌的第一部分。

**举例:**

```
1.{
2\. "alg":"SHA256",
3\. "typ":"JWT" 
4.}
```

# 有效载荷

有效负载是令牌的第二部分，它有声明。这些语句包含与用户相关的实体或额外数据。

# 我们可以在令牌中添加不同的声明，这里有三种类型:

*   **注册声明**是预定义的，它们并不是强制性的，但建议使用它们。比如受众(aud)、到期时间(exp)等。由于 JWT 很紧凑，您会发现索赔的名称仅由三个字符组成。
*   在定义**公共声明**时，我们需要消除冲突，为此你可以通过 URI(包含抗冲突的名称空间)或 [IANA JSON Web Token Registry](https://www.iana.org/assignments/jwt/jwt.xhtml) 来定义公共声明。
*   正是通过私人声明，我们在同意使用这些声明的双方之间共享数据。这些既不是公开的也不是登记的索赔。

有效载荷的安全性很弱，因为任何人都可以毫不费力地解码 JWT 并看到有效载荷的内容。因此，我们不应该将任何敏感和秘密的信息放在有效载荷中。如果需要，在添加到有效负载之前加密数据。

**有效载荷看起来是这样的:**

```
1.{ 
2."jti": "359a2088-2276-48c3-8375-378802e039af", 
3."iat": 1617631282, 4."http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "1413f2f2-5214-413f-8b12-d09cb9a8fa7b", 5."http://schemas.microsoft.com/ws/2008/06/identity/claims/role":
"Admin", 
6."nbf": 1617631282,
7."exp": 1712239282,
8."iss": "MyProject", 
9."aud": "https://www.desuvit.com/" 
10.}
```

尽管有效负载是用户识别的唯一识别因素，但它并不验证用户。在这种情况下，任何一知半解的用户都可以找到信息并创建一个假令牌。因此，我们利用签名，这是 JWT 中的关键认证因素，以确保信息不会在途中被更改。

# 哈希算法

**但是，在我们跳到签名之前，让我们来看看哈希算法。**

在哈希算法的帮助下，我们可以将任何字符串转换成哈希函数。例如， **Hello，world** 可以通过 SHA265 算法转换成 hash。这些算法有一个重要的特性，它不允许你借助你有的散列来找出原始字符串。

这就是说，如果你已经把 **Hello，world** 转换成 hash，那么你就不能用那个 hash 去找字符串 **Hello，world** 。

# JWT 签名

**在 JWT 签名中，我们利用了三样东西:**

首先是算法的名字，比如 HMACSHA256，它是从 SHA265 派生出来的。

其次，编码的头和有效载荷字符串被转换成散列。

最后，使用一个只有服务器知道的密钥。它包含了我们无法解码的任意数据。

我们对整个 JWT(包括有效载荷和报头)进行编码或使用哈希的原因是为了确保一个特定令牌的签名的唯一性。

# 用于生成 JWT 令牌的 Rest API

通常在 ReactJS 应用程序中，我们使用 Rest API 来获取数据或保存数据。即使是登录用户，我们也使用 API 端点。对于博客的其余部分，我们假设 API 端点会在用户通过身份验证后返回 JWT。我们将在任何后续请求中使用这个 JWT 令牌。

# 使用 Rest API 进行身份验证

到目前为止，我们已经了解了 JWT 及其结构。我们将如何在 API 的认证过程中使用它？

**让我们来看看:-**

# 登录

*   每当用户通过向 Rest API 端点提供凭证(通常是用户名和密码)进行登录时，就会在服务器上生成一个 JWT 令牌并发送回客户端。
*   一旦客户端收到令牌，他们就可以在将来的任何请求中使用它，以便通过正确的身份验证来识别用户。
*   我们将从 API 中获取 JWT 令牌，我们将把它存储在会话中，以便在需要时进一步使用。调用 API 并将令牌存储在会话存储中的代码如下所示。在这种方法中，我们使用 redux saga。

```
1.export function* loginFlow() { 
2.const check = true;
3\. while (check) { 
4.const { email, password } = yield take(LOGIN_REQUEST);
5.
6\. const token = yield apiCall(Api.login, email, password);
7.
8\. if (token) { 
9.sessionStorage.setAuthToken(JSON.stringify(token));
10\. const parsedAccessToken = parseJwtToken(token.auth_token); 
11.constrole=parsedAccessToken["http://schemas.microsoft.com/ws/2008/06/identity/claims/role"];
12\. yield put(setLoginSuccess());
13\.     
14      } 
15\.   } 
16\. }
```

同样在上面这段代码中，我们解析 JWTToken 来获取用户的角色，该角色被称为 claim。

**下面是解析 JWT 令牌的代码，**

```
1.function parseJwt(token) { 
2.const base64Url = token.split('.')[1];
3.const base64 = base64Url.replace(/-/g, '+').replace(/_/g, '/'); 4.return JSON.parse(window.atob(base64)); 
5.}
```

# 当我们的服务器收到通过授权令牌对用户进行身份验证的请求时，会发生以下情况:

*   令牌被解码，并且从有效载荷中提取 ID。
*   在用户数据库中检查该用户 ID。
*   现在对请求令牌和用户模型令牌进行比较。如果匹配，用户的身份验证就完成了。否则，用户就不能访问。

# 注销

当 JWT 令牌被创建时，我们也应该为其设置一些到期时间。通过这种方式，令牌在特定的时间框架后失效。还有一种机制，通过该机制可以刷新令牌，以避免令牌的任何滥用。

# ReactJS 专用路线

在 ReactJS 中，路由器是一个重要的组件，它有助于同步您的 URL 和 UI。在本节中，我们将了解如何使用私有和公共路由。

# 让我们先来看看一个简单应用程序的结构:

*   每个人都可以访问主页/登录/注册页面，因此这是一条公共路径。
*   经过身份验证的用户可以访问仪表板/我的帐户页面，因此这是一条私人路线。

# 私人路线

在 PrivateRoute 组件中，包含了您的应用程序的每条路线。当用户登录后，他们可以看到这条路线的组成部分。例如，仪表板。如果该用户未登录，他们将被重定向或转到登录页面。

我们可以定义如下所示的专用路由。正如您在代码中所看到的，我们获得了在会话中派生和存储的角色，如上一节所述。根据角色，我们授予对组件的访问权限，或者重定向到登录屏幕

```
1.export const PrivateCandidateRoute = ({ component: Component, ...rest }) => {
2\. const { location } = rest; 
3.const role = helper.fetchRole();
4.let isCandidate = false;
5.if (role == constant.CANDIDATE_ROLE) { 
6\.  isCandidate = true; 
7.} 
8.const adminRol = isCandidate == true ? true : false;
9\. return ( 
10\.    <Route {...rest} render={(props) => 
11\.       adminRol ? 
12\.           <Component {...rest} {...props} /> :
13\.                <Redirect to={{ 
14\.                     pathname: constant.LOGIN_SCREEN, 
15\.                       state:{
16\.                          prevLocation: location 
17\.                       } 
18\.                  }} /> 
19\.     } />
20\.   );
21\. };
```

# 公共路线

PublicRoute 提供对受限组件和公共组件的访问。公共路由可能如下:-

```
<Route path={constant.SEARCH_SCREEN} render={() <SearchScreenContainer {...props} />} />
```

# 摘要

一旦任何用户登录到 REST API，就会创建 JWT 令牌。在这个令牌的帮助下，提取所需的角色，这有助于提供和显示用户有权查看的页面。如果获得授权，该用户也可以看到私人航线的网页。

[德苏威](https://www.desuvit.com/implementing-authentication-and-authorization-in-react-js-a-stepwise-guide/www.desuvit.com)在移动应用程序开发方面拥有多年的经验和专业知识。[联系我们](https://share.hsforms.com/1E6PleoXCRFSK1W8R7ockaw44fu7)了解更多。

> **Desuvit 是一家诞生于挪威的软件开发公司，为各种垂直行业和业务领域提供定制软件开发、移动应用和 Web 应用开发。**
> 
> **我们为没有软件或 IT 部门的公司、初创公司以及需要扩大软件开发规模但缺乏所需专业知识的公司提供端到端解决方案。我们帮助客户发展业务，因此我们可以与他们一起成长。**
> 
> **我们使用的一些技术:。Net、Azure、微服务、Azure functions(无服务器计算)、React Native、Flutter、React JS、TypeScript、Angular、NServiceBus、Azure Service Bus、Azure Queues、SQL Service、MySQL、Cosmos DB 等。**
> 
> [**联系我们**](https://www.desuvit.com/contact-us/)**&**[**获取您的项目预估**](https://share.hsforms.com/1E6PleoXCRFSK1W8R7ockaw44fu7)

> *最初发表于 2021 年 8 月 1 日*[*https://www.desuvit.com*](https://www.desuvit.com/implementing-authentication-and-authorization-in-react-js-a-stepwise-guide/)*。*