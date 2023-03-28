# JWT 认证-101

> 原文：<https://medium.com/codex/jwt-auth-101-e78aeef640c?source=collection_archive---------12----------------------->

## 如何处理不同服务之间的认证？

现在在做关于微服务的研究。实际上，我理解了一些关键点。然而，有一个模糊点，如“我如何使用 auth service 为我的 post 服务处理认证”。在本文中，我们将通过 Python、Flask 中的应用示例来了解如何处理不同系统之间的认证。

![](img/42aa1662a4a66effba9767947f6e4494.png)

[Growtika 开发商营销机构](https://unsplash.com/@growtika?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/authentication-microservices?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 什么是认证和授权？

认证是识别一个**用户在我们的系统中是否存在**的过程。

授权是确定一个**用户是否有权限**执行特定操作或访问特定资源的过程。

换句话说，在对用户进行身份验证并确认其身份后，授权是检查是否允许该用户执行特定操作或访问特定资源的过程。例如，用户可能已经过身份验证并登录到系统，但他们可能无权访问某些敏感信息或执行某些操作，如修改关键的系统设置。

# 什么是 JWT？

JWT 是一种在双方之间传输信息的紧凑且独立的方式。它通常用于身份验证和授权，并允许各方安全地交换信息，如用户身份和其他声明。JWT 通常由一个 JSON 对象组成，该对象经过数字签名并可选地加密，并且通常作为 **HTTP 授权头**中的**承载令牌**发送。

“如果你是第一次听说 JWT，你可以访问 jwt.io 了解更多。”

[](https://jwt.io/) [## JWT。超正析象管(Image Orthicon)

### JSON Web Token (JWT)是一种简洁的、URL 安全的方式，用于表示要在双方之间传输的声明。的…

jwt.io](https://jwt.io/) 

# 理论上的 JWT 认证

1.  用户向身份验证服务的登录端点发送请求。

*   我使用[“HTTP Basic”](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#authentication_schemes)发送登录请求，可以使用请求体或者其他方法。

2.服务解析请求的授权头，并检查用户是否存在于系统中。

*   如果没有用户，返回错误信息和 [401 状态码](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401)。
*   如果用户在系统中存在，则创建 JWT 并返回令牌。

3.通过响应获得行动。

*   如果有令牌，将令牌放入 HTTP 授权头(Bearer <token>)。</token>

4.使用令牌请求其他服务。

![](img/f58e355634f847b57d95746130412cad.png)

作者图|你见过的最差的图之一:)

# JWT 认证在行动

在这一节中，我们将通过使用 Flask 来探索这一理论，Flask 是 Python 生态系统的一个 web 框架。

## 要求

我们将在项目中使用以下包。

*   `flask`:网络服务器(`pip install flask`)
*   `pyjwt`:生成令牌(`pip install pyjwt`)
*   `httpx`:向端点发出请求(`pip install httpx`)

## 授权服务

首先，我要定义常数。

```
"""
# We use the snippets below to make requests

# login post request
- httpx -m POST --auth user@user.com Passw0rd http://127.0.0.1:5000/login

# validate post request
- httpx -m POST -h "Authorization" "Bearer <TOKEN>" http://127.0.0.1:5000/validate
"""
from flask import Flask, request
import jwt
import datetime

JWT_SECRET = (
    "this-is-so-secret-key-to-encode-jwt-tokens"  # you should get this by using ENV
)
USERS_DB = [
    {"username": "user@user.com", "password": "Passw0rd", "is_admin": True}
]  # you should use a real db :)

app = Flask(__name__)
```

`app`将引用服务器。

`JWT_SECRET`将在 JWT 创建过程中使用。

`USERS_DB`将被用作虚拟数据库。我选择这种方法是为了保持简单。

```
 @app.route("/login", methods=["POST"])
def login():
    print(request.headers)  # We need to get "Authorization" header of the request
    print(request.authorization)  # This is automatically decode the header in Flask

    # handle username and password from request header (Flask does for us, it decodes)
    auth = request.authorization
    if not auth:
        return "missing credentials", 401

    # filter users
    users_list = [u for u in USERS_DB if u["username"] == auth.username]
    # is there any user with the credentials
    if len(users_list) != 1 or users_list[0]["password"] != auth.password:
        return ("Missing credentials", 401)
    else:
        return createJWT(auth.username, JWT_SECRET, users_list[0]["is_admin"]), 200
```

正如我前面提到的，我们需要处理请求的`Authorization`头。为此，我们可以使用烧瓶中的`request.headers`。我将使用`httpx`发送一个 POST 请求来模拟上面的代码。

```
httpx -m POST --auth user@user.com Passw0rd http://127.0.0.1:5000/login
```

我们将得到如下输出。

```
Host: 127.0.0.1:5000
Content-Length: 0
Accept: */*
Accept-Encoding: gzip, deflate
Connection: keep-alive
User-Agent: python-httpx/0.23.1
Authorization: Basic dXNlckB1c2VyLmNvbTpQYXNzdzByZA==
```

正如你看到的`Authorization`头，我们得到一个编码值。

> 你可以查看 RFC 7617 -基本的 HTTP 认证方案

我们可以通过使用`request.authorization`得到它的解码值。

```
{'username': 'user@user.com', 'password': 'Passw0rd'}
```

Flask 为我们解码了授权头。我们在下面的代码块中看到了 Flask 解码代码。

```
# Flask authorization header parser
def parse_authorization_header(
    value: t.Optional[str],
) -> t.Optional["ds.Authorization"]:
    """Parse an HTTP basic/digest authorization header transmitted by the web
    browser.  The return value is either `None` if the header was invalid or
    not given, otherwise an :class:`~werkzeug.datastructures.Authorization`
    object.

    :param value: the authorization header to parse.
    :return: a :class:`~werkzeug.datastructures.Authorization` object or `None`.
    """
    if not value:
        return None
    value = _wsgi_decoding_dance(value)
    try:
        auth_type, auth_info = value.split(None, 1)
        auth_type = auth_type.lower()
    except ValueError:
        return None
    if auth_type == "basic": # We used this scheme by sending request with httpx 
        try:
            username, password = base64.b64decode(auth_info).split(b":", 1) # httpx encodes the header for us
        except Exception:
            return None
        try:
            return ds.Authorization(
                "basic",
                {
                    "username": _to_str(username, "utf-8"),
                    "password": _to_str(password, "utf-8"),
                },
            )
        except UnicodeDecodeError:
            return None
    elif auth_type == "digest":
        auth_map = parse_dict_header(auth_info)
        for key in "username", "realm", "nonce", "uri", "response":
            if key not in auth_map:
                return None
        if "qop" in auth_map:
            if not auth_map.get("nc") or not auth_map.get("cnonce"):
                return None
        return ds.Authorization("digest", auth_map)
    return None
```

在检查系统中是否存在该用户后，我们可以使用我们的`createJWT`函数创建 JWT。我们可以扩展我们想要的有效载荷。

> `admin`创建有效负载的密钥是为了处理“授权”,这不在本文的讨论范围内。

```
def createJWT(username, secret, authz):
    payload = {
        "username": username,
        "exp": datetime.datetime.now(tz=datetime.timezone.utc)  # expire date
        + datetime.timedelta(days=1),  # token lifetime is 1 day
        "iat": datetime.datetime.utcnow(),  # issued at
        "admin": authz,  # is user admin
    }
    return jwt.encode(
        payload,
        secret,
        algorithm="HS256",
    )  # return JWT token
```

如果我们成功地向我们的登录端点发出请求，我们将获得 JWT 令牌。

![](img/e5f42b63a51cd7b7991c5a85c6fd6cb0.png)

作者图片

现在我们已经准备好使用我们的验证端点来验证令牌了。

```
@app.route("/validate", methods=["POST"])
def validate():
    encoded_jwt = request.headers[
        "Authorization"
    ]  # handle "Authorization" header. We have to send the token in this scheme => Bearer <TOKEN>

    if not encoded_jwt:
        return ("missing credentials", 401)

    encoded_jwt = encoded_jwt.split(" ")[1]  # get the token (exclude "Bearer")

    try:
        decoded = jwt.decode(
            encoded_jwt, JWT_SECRET, algorithms=["HS256"]
        )  # decode the token
    except:
        return ("not authorized", 403)

    return (decoded, 200)
```

如果我们能够成功地向我们的验证端点发出请求，我们需要解码 JWT。

```
httpx -m POST -h "Authorization" "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6InVzZXJAdXNlci5jb20iLCJleHAiOjE2NzA4NDY4NjIsImlhdCI6MTY3MDc2MDQ2MiwiYWRtaW4iOnRydWV9.Z3bA45Q414VsO9GYECsr9YO40ac1xD-EuMz5TeUOroA" http://127.0.0.1:5000/validate
```

![](img/f99d96e0702175431d65a8887a7f49c9.png)

作者图片

我们已经成功开发了我们的认证服务。现在我们需要创建我们的 post 服务。**请记住，我们将使用来自 post 服务的验证和登录端点来验证用户。**

## 网关服务

我们将从定义常数开始。

```
"""
# We use the snippets below to make requests

# login post request
- httpx -m POST --auth user@user.com Passw0rd http://127.0.0.1:8080/login

# GET Posts
- httpx -m GET -h "Authorization" "Bearer <TOKEN>" http://127.0.0.1:8080/posts

# POST Posts
- httpx -m POST -h "Authorization" "Bearer <TOKEN>" -j '{"id":1, "title": "Title Text"}' http://127.0.0.1:8080/posts
"""
from flask import Flask, request
import httpx
import json

AUTH_SERVICE_URL = "http://127.0.0.1:5000"  # you should get this by using ENV
POSTS_DB = []  # to simulate inserting

app = Flask(__name__)
```

我们将使用`AUTH_SERVICE_URL`在我们的 making requests 函数中设置前缀。

`POSTS_DB`是我们的虚拟数据库。

`app`是服务器。

> 实际上，在这里我试图模拟 API 网关架构。但是，为了简单起见，我还将 post 服务合并到了 gateway 服务中。因此，在我的例子中，只有一个额外的服务来模拟 posts & gateway。

首先，我将从解释 posts 服务中的网关功能开始。

```
 def auth_service_login(request):
    """Auth request to auth service

    Args:
        request (request): Flask request

    Returns:
        tuple: First item of the tuple will be the token if the request is successfull and 2nd item will be the status code
    """
    auth = request.authorization
    if not auth:
        #  if there is no "Authorization" header in the request that is coming to "gateway" service, return error
        return None, ("missing credentials", 401)

    basicAuth = (auth.username, auth.password)

    # post request to auth service to get the token
    response = httpx.post(f"{AUTH_SERVICE_URL}/login", auth=basicAuth)

    if response.status_code == 200:
        return response.text, None
    else:
        return None, (response.text, response.status_code)
```

这个`auth_service_login`功能通过将 post 服务请求重定向到 auth service 来帮助我们登录。如果成功，我们将有一个 JWT，没有错误。如果不成功，我们将没有令牌和错误。

```
def auth_service_validate_token(request):
    """Token validation in auth service

    Args:
        request (request): Flask request

    Returns:
        tuple: First item of the tuple will be the decoded token if the request is successfull and 2nd item will be the status code
    """
    if not "Authorization" in request.headers:
        # if there is no "Authorization" header in the request that is coming to "gateway" service, return error
        return None, ("missing credentials", 401)

    token = request.headers["Authorization"]

    if not token:
        # if there is no JWT token return error
        return None, ("missing credentials", 401)

    # post request to auth service to validate the token
    response = httpx.post(
        f"{AUTH_SERVICE_URL}/validate",
        headers={"Authorization": token},
    )

    # auth service returns us the token and status code
    if response.status_code == 200:
        return response.text, None
    else:
        return None, (response.text, response.status_code)
```

这个`auth_service_validate_token`函数将 post 服务请求重定向到 auth 服务验证端点。与`auth_service_login`功能的工作方式相同。

现在我们已经准备好实现这个服务的端点了。

```
 @app.route("/login", methods=["POST"])
def login():
    # get the token by using gateway service (we can think that the request will be redirected to the auth service)
    token, err = auth_service_login(request)
    if not err:
        return token
    else:
        return err

@app.route("/posts", methods=["POST"])
def post_posts():
    # firstly validate the token by using gateway service. By doing that we enforce to make the user to be logged in
    token, err = auth_service_validate_token(request)
    if err:
        return err
    else:
        POSTS_DB.append(request.json)
        return {"msg": "success", "data": request.json}

@app.route("/posts", methods=["GET"])
def get_posts():
    # firstly validate the token by using gateway service. By doing that we enforce to make the user to be logged in
    token, err = auth_service_validate_token(request)
    if err:
        return err

    token = json.loads(token)  # we get decoded token

    return {"posts": POSTS_DB}
```

我们准备试验网关服务。

## 网关测试

我将向网关服务的登录端点发出请求。

> 我通过使用不同的端口号来分离服务。`5000`用于`auth`服务，`8080`用于`gateway`服务。

```
httpx -m POST --auth user@user.com Passw0rd http://127.0.0.1:8080/login
```

![](img/19208529dd17261868b04decdcf31010.png)

作者图片

现在，我们可以通过使用从网关登录端点获得的令牌来获得帖子列表。

```
httpx -m GET -h "Authorization" "Bearer <TOKEN>" http://127.0.0.1:8080/posts
```

![](img/2d5df493d9669ca25ef48613e29bddbc.png)

作者图片

我们可以添加新的帖子。

```
httpx -m POST -h "Authorization" "Bearer <TOKEN>" -j '{"id":1, "title": "Title Text"}' http://127.0.0.1:8080/posts

httpx -m POST -h "Authorization" "Bearer <TOKEN>" -j '{"id":2, "title": "Post 2"}' http://127.0.0.1:8080/posts

httpx -m POST -h "Authorization" "Bearer <TOKEN>" -j '{"id":3, "title": "Post 3"}' http://127.0.0.1:8080/posts
```

![](img/d559b3389bcdb260ca711ba84c133c8e.png)

作者图片

如果一切顺利，我们应该会看到我们添加的帖子。

```
httpx -m GET -h "Authorization" "Bearer <TOKEN>" http://127.0.0.1:8080/posts
```

![](img/ae69eba418414ed8e5a6834ad3907306.png)

作者图片

# 最后

希望你喜欢并觉得有帮助。我想增强自己的知识，学习身份认证的基础知识。我不再只是复制和粘贴代码来使它运行(老实说，我已经没有这样做🥲).

你可以通过下面的链接找到我们在文中使用的代码。

[](https://github.com/mebaysan/Auth-101) [## GitHub - mebaysan/Auth-101:不同服务之间的基本 JWT 认证

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/mebaysan/Auth-101) 

此外，你可以订阅我的 Youtube 频道来观看视频中的文字🤓

[](https://www.youtube.com/@BaysanSoft) [## BaysanSoft

### 本·穆罕默德·埃内斯·巴伊桑·梅尔哈巴拉尔。亚萨姆·于波。“i̇lmin…

www.youtube.com](https://www.youtube.com/@BaysanSoft) 

问候