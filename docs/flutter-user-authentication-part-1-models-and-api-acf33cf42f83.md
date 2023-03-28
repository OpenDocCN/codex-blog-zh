# Flutter 用户认证第 1 部分:模型和 API

> 原文：<https://medium.com/codex/flutter-user-authentication-part-1-models-and-api-acf33cf42f83?source=collection_archive---------0----------------------->

![](img/9662b0522e9dd3d43d0fa8d502188081.png)

Artur Shamsutdinov 在 [Unsplash](https://unsplash.com/s/photos/flutter?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

用户注册、进出几乎是每种应用的通用功能。在这个系列中，我将教你如何构建一个简单的认证系统。第 1 部分将介绍 API 调用和模型的基础知识。在第 2 部分中，我将教你如何使用 Cubit 包在你的应用程序中存储认证用户，在第 3 部分中，我们将学习如何在应用程序关闭后使用共享首选项包来维护登录 ***。***

播客:[https://podcast . apple . com/us/podcast/coreys-corner/id 1479097455](https://podcasts.apple.com/us/podcast/coreys-corner/id1479097455)

一定要看看我的 [Flutter UI 教程](https://www.youtube.com/watch?v=89jsvjHhBp4)！

# 创建基本 API 类:

我们的第一步是构建一个 BaseAPI 类来保存我们 API 的所有 URL。在我的 [*如何让 Flutter API 调用变得简单*](https://c0reygardner63.medium.com/making-flutterapi-calls-easy-38ef7ea4e5d4) 中，我教了你如何使用类继承来简化和组织你的 API 调用。这个类并不复杂，它只是存储我们将要请求的路线，查看下面的代码。

```
Class BaseAPI{ static String base = "http://localhost:3000"; 
    static var api = base + "/api/v1";
    var customersPath = api + "/customers";
    var authPath = api + "/auth"; 
   // more routes Map<String,String> headers = {                           
       "Content-Type": "application/json; charset=UTF-8" };                                      

}
```

最终创建我们的基类使我们更容易管理我们的 API 端点。

# **创建客户 API 类**

接下来，我们将创建一个类来存储所有用于客户身份验证的 API 调用。

我们将使用 darts [HTTP 库](https://flutter.dev/docs/cookbook/networking/fetch-data)发出请求，我们发送的任何数据都将以 JSON 格式编码。每个请求将返回一个 HTTP 响应类型的 Future。在我们的小部件中，我们将使用响应的 statusCode 属性来确定我们的调用是否成功。

```
import 'dart:convert';

import 'package:resteraunt_starter/api/BaseAPI.dart';
import 'package:http/http.dart' as http;

class AuthAPI extends BaseAPI { Future<http.Response> signUp(String name, String email, String phone,
      String password, String passwordConfirmation) async {
    var body = jsonEncode({
      'customer': {
        'name': name,
        'email': email,
        'phone': phone,
        'password': password,
        'password_confirmation': passwordConfirmation
      }
    });

    http.Response response =
        await http.post(super.customersPath, headers: super.headers, body: body);
    return response;
  }

  Future<http.Response> login(String email, String password) async {
    var body = jsonEncode({'email': email, 'password': password});

    http.Response response =
        await http.post(super.authPath, headers: super.headers, body: body);

    return response;
  }

  Future<http.Response> logout(int id, String token) async {
    var body = jsonEncode({'id': id, 'token': token});

    http.Response response = await http.post(super.logoutPath,
        headers: super.headers, body: body);

    return response;
  }

}
```

现在是时候创建我们的客户类了！

# 创建客户对象

当我们创建一个对象时，我们就创建了我们自己的数据类型，我们创建了一个蓝图，概述了我们每个客户将拥有的所有属性。

```
import 'dart:convert';

class Customer{
  int id;
  String email;
  String phone;
  String name;
  String token;

  User({this.id, this.email, this.phone, this.name, this.token});

  factory Customer.fromReqBody(String body) {
    Map<String, dynamic> json = jsonDecode(body);

    return Customer(
      id: json['id'],
      email: json['email'],
      name: json['name'],
      phone: json['phone'],
      token: json['token'],
    );

  }

  void printAttributes() {
    print("id: ${this.id}\n");
    print("email: ${this.email}\n");
    print("phone: ${this.phone}\n");
    print("name: ${this.name}\n");
    print("token: ${this.token}\n");
  }

}
```

我们做的第一件事是创建类构造函数来初始化客户对象的新实例。我们使用**工厂构造函数，因为可能有一段时间我们不需要创建一个全新的类实例。**我们的工厂方法将从我们的 API 调用请求体接收一个 JSON 对象，我们将把它解码成一个动态类型字符串的映射&。从他们那里，只需要将我们的客户属性设置为映射中相应的键。最后， **printAttributes()** helper 方法将打印出所有的属性及其值，这对调试非常有用。

# 在我们的小部件中

## ../身份验证/身份验证. dart

```
class Auth extends StatefulWidget {
  @override
  _AuthState createState() => _AuthState();
}

class _AuthState extends State<Auth> {
  bool showSignUp = true;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        appBar: AppBar(
            title: Text(
              "Corey's Corner",
            ),
          elevation: 16.0,
          actions: [
            IconButton(
                icon: Icon(Icons.swap_horiz),
                onPressed: () {
                  setState(() { showSignUp = !showSignUp;
                  });
                })
          ],
        ),
        // ternary operator 
      body: Container(child: showSignUp ? SignUp() : SignIn()));
  }
}
```

在这个小部件中，我们设置了一个容器来存放我们的 Signup() & SignIn()小部件。我们使用一个布尔函数在不同的页面之间来回切换，这样可以避免用户不得不编写 push 函数来访问不同的页面。

## ../authentication/sign_in .镖

为了简洁起见，我将把所有的表单、文本和按钮样式从图片中去掉，本教程将只讨论登录页面。

```
class SignIn extends StatefulWidget {

  @override
  _SignInState createState() => _SignInState();
}

class _SignInState extends State<SignIn> {
  AuthAPI _authAPI = AuthAPI();
  final _key =  GlobalKey<FormState>();
  String email;
  String password;
  @override
  Widget build(BuildContext context) {

    return  Container(
        padding: EdgeInsets.symmetric(vertical: 20.0, horizontal: 25.0),
        child: Form(
            key: _key,
            child: Column(
              mainAxisAlignment: MainAxisAlignment.start,
              children: <Widget>[
                SizedBox(height: 70),
                Text("Sign In", style: formTitleStyle(),),
                SizedBox(height: 30),
                Container(
                    width: 400,
                    child: TextFormField(
                      decoration: textInputDecoration("Email", context),
             onChanged: (val) => setState(() => email = val),
                    )
                ),
                SizedBox(height: 30),
                Container(
                  width: 400,
                  child: TextFormField(
                    obscureText: true,
                    decoration: textInputDecoration("Password", context),
                    onChanged: (val) => setState(() => password = val),
                  ),
                ),
                SizedBox(height: 25),
                GestureDetector(
                  child: Text("Forgot Password ?", style: TextStyle(
                      fontSize: 18.0,
                      decoration: TextDecoration.underline
                  ),),
                  onTap: (){
                  // todo 
                  },
                ),
                SizedBox(height: 25),
                Container(
                    width: 400,
                    child: customRaisedIconButton("Sign In !", Icons.send, context, () async {
                      if(_key.currentState.validate()){
                        try{
                          var req = await 
                       _authAPI.login(email,  password);
                          if(req.statusCode == 200){print(req.body);
var customer = 
                              Customer.fromReqBody(req.body);customer.printAttributes();
                        Navigator.push(context, MaterialPageRoute(
                         builder: (context) => MyHomePage(customer:                 customer)));
                          } else {
                            pushError(context);
                          }
                        } on Exception catch (e){
                        print(e.toString());
                        pushError(context);
                        }
                      }
                    })
                )
              ],
            )
        )
    );
  }
void PushError(){
    Navigator.push(context, MaterialPageRoute(
        builder: (context) => Error()
    ));
}
```

我们要做的第一件事是在状态中创建电子邮件和密码字符串。在文本表单中，我们调用 setState 将有状态字段设置为客户输入的值。在我们的 API 调用之前，我们将使用验证器来确保我们的电子邮件和密码不被泄露，这样我们就不会进行任何必要的 API 调用。

```
AuthAPI _authAPI = AuthAPI();
```

在这行代码中，我们初始化了 AuthAPI 对象的一个实例，并将其存储在一个变量中。

我们的 API 调用是*异步的*，因为我们必须等待我们的数据。我们使用 *await* 语句来等待我们的请求。**异步编程允许我们的代码非线性执行。**我们将调用封装在 try 语句中，以捕捉任何错误，我们调用登录函数，并通过代码行将存储在 state 中的对象传递给它:

```
var req = await _authAPI.login(email,  password);
```

一旦我们收到请求，我们就使用它的 statusCode 属性来决定下一步做什么。如果我们的代码读取 200，我们将把请求体属性(JSON 类型)传递给客户模型的工厂构造函数。在那里，我们打印出用户的新属性、请求体并推送到主页。如果我们没有收到正确的 statusCode 或者我们捕捉到一个异常，我们就会进入一个错误页面。

感谢阅读！在下一篇文章中，我们将讨论如何使用 [Cubits](https://pub.dev/packages/flutter_bloc) 在我们的应用程序中存储我们的客户，使其对我们所有的小部件可用。

务必检查[第二部分](/codex/flutter-user-authentication-part-2-storing-users-with-the-cubit-6d60c537ce82)和[第三部分](https://c0reygardner63.medium.com/flutter-user-authentication-part-3-persistent-login-with-shared-preferences-d0ffb64e1745)！

学习编码:[https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ](https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ)

播客:[https://podcast . apple . com/us/podcast/coreys-corner/id 1479097455](https://podcasts.apple.com/us/podcast/coreys-corner/id1479097455)