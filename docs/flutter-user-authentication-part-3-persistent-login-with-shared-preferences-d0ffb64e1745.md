# Flutter 用户认证第 3 部分:共享首选项的持久登录

> 原文：<https://medium.com/codex/flutter-user-authentication-part-3-persistent-login-with-shared-preferences-d0ffb64e1745?source=collection_archive---------2----------------------->

![](img/2cf138c5415ca66cebe84504e1f50193.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/settings?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄

在本系列的[第一部分](/codex/flutter-user-authentication-part-1-models-and-api-acf33cf42f83)中，我教了您如何构建一个[客户模型和一个认证 API](/codex/flutter-user-authentication-part-1-models-and-api-acf33cf42f83) ，在[第二部分](/codex/flutter-user-authentication-part-2-storing-users-with-the-cubit-6d60c537ce82)中，我们学习了如何使用 [Cubit](https://pub.dev/packages/flutter_bloc) 来管理整个应用程序的[登录状态。现在，在这个系列的最后一部分，我们将学习如何**保持持久登录**甚至当我们的应用程序关闭时。我们开始吧！](/codex/flutter-user-authentication-part-2-storing-users-with-the-cubit-6d60c537ce82)

科里的角落播客:[https://anchor.fm/coreys-corner](https://anchor.fm/coreys-corner)

一定要检查我的[颤振 UI 教程](https://www.youtube.com/watch?v=MZmSZWEfGBc)！

## 共享偏好

根据官方文件,[共享偏好](https://pub.dev/packages/shared_preferences)是一个库

> 为简单数据包装特定于平台的持久存储(iOS 和 macOS 上的 NSUserDefaults，Android 上的 SharedPreferences 等)。).数据可能会异步持久化到磁盘，并且不能保证写操作返回后会持久化到磁盘，**所以这个插件不能用于存储关键数据**。

本质上，这个包将允许我们在运行我们的应用程序的设备上存储和检索信息。

请注意，共享首选项包明确指出，关键数据不应该以这种方式存储。我们构建的应用程序仅用于生产和演示目的。如果你想在生产环境中保持持久登录，我推荐使用 [flutter_secure_storage](https://pub.dev/packages/flutter_secure_storage) 包，它的操作方式非常相似。

## 游戏计划

当用户登录我们的应用程序时，我们将把他们的 API 令牌和 ID 存储到磁盘中。每次打开应用程序时，我们都会检查这些值是否存在。如果它们存在，我们将发出一个 API 请求，然后我们将获取数据来创建我们的客户模型实例，通过 Cubit 登录我们的客户。

## 使用共享偏好设置存储数据

是时候创建一个函数来将我们客户的 API 令牌和 ID 保存到磁盘上了:

```
../prefs.dart
import 'package:shared_preferences/shared_preferences.dart';/* 
don't forget to add shared_preferences to pubspec.yaml before following along
*/void upDateSharedPreferences(String token, int id) async {
  SharedPreferences _prefs = await SharedPreferences.*getInstance*();
  _prefs.setString('token', token);
  _prefs.setInt('id', id);
}
```

共享首选项的操作就像一个映射或字典，其中包含特定于类型的键/值对。首先，我们创建一个共享偏好 ie 的实例。_prefs。然后，我们使用 setString 和 sentInt 方法将整数和字符串保存到磁盘。每个 set 函数的第一个参数是我们存储的键，而第二个参数是与每个键相关的值。

## 登录后更新共享偏好设置

回想一下在[第 1 部分](/codex/flutter-user-authentication-part-1-models-and-api-acf33cf42f83)中，我们是如何创建一个进行 API 调用的登录表单的。当 API 请求成功时，我们使用工厂方法从 API 调用返回的 JSON 数据中创建客户模型的实例。在成功创建了一个 Customer 实例之后，我们所要做的就是调用我们刚刚创建的函数，用刚刚登录的客户的 ID 和令牌来更新我们的共享首选项。下面是我们的登录和注册功能的示例:

```
../sign_in.dartContainer(
    width: 400,
    child: customRaisedIconButton("Sign In !", Icons.*send*, context, () async {
      if(_key.currentState.validate()){
        try{
          var req= await _authAPI.createSession(email, password);
          if(req.statusCode == 202){
            var customer = Customer.fromReqBody(req.body);//checkout part II & II if you're not familiar with BlocProvider or //Cubits BlocProvider.*of*<CustomerCubit>(context).login(customer);
            upDateSharedPreferences(customer.token, customer.id);
           Navigator.*push*(context, MaterialPageRoute(builder: (context) => MyHomePage(
           )));
          } else {
            pushError(context);
          }
        } on Exception catch (e){
          pushError(context);
        }
      }
    })
```

## 持续登录

这是你一直在等待的时刻…实现持久登录。在呈现我们的应用程序的根小部件(在本例中是 HomePage)之前，我们需要检查共享的首选项是否存在。

我们将这个[异步](https://dart.dev/codelabs/async-await)函数称为 checkPrefsForUser，我们再次创建了一个共享偏好的实例。然后，我们使用 getString 和 getInt 方法检索保存的数据。如果数据不存在，则没有用户登录，这些值将为 null 类型。

一旦我们确定这些值是否存在，我们就发出一个 API 调用来完成从后端检索关于我们客户的所有 JSON 数据。我们再次使用一个[工厂构造函数](https://dart.dev/guides/language/language-tour#factory-constructors)来创建一个客户实例，并使用 Cubit 包管理全局登录状态。为了安全起见，我们用客户的 ID &令牌重新填充我们的共享首选项。

只有当我们的客户 BlocBuilder 的状态为 null 时，我们才在呈现主页小部件之前运行这个函数。

```
class MyHomePage extends StatelessWidget {
  const MyHomePage({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    AuthAPI _authAPI = AuthAPI();
    return BlocBuilder<CustomerCubit, Customer>(
      buildWhen: (previous, current) => previous != current,
        builder: (BuildContext context, Customer state){
          checkPrefsForUser() async {
          SharedPreferences _prefs = await          SharedPreferences.*getInstance*();
          var _sharedToken = _prefs.getString('token');
          var _sharedId = _prefs.getInt('id');
          if(_sharedToken != null && _sharedId != null){
            try{
              var req = await _authAPI.getUser(_sharedId, _sharedToken);
              if(req.statusCode == 202){
                var user = User.fromReqBody(req.body);
                BlocProvider.*of*<UserCubit>(context).login(user);
                upDateSharedPreferences(user.token, user.id);
              }
            }on Exception catch (e){}
          }
        }
        if(state == null){
          checkPrefsForUser();
        }
      return Scaffold(
```

**注销用户**

要注销用户，我们只需要将我们的 Cubit 设置为 nil，发出 API 请求，并从我们的共享首选项中删除我们之前存储的值。这是一个图标按钮的例子，我们可以用它来做这件事:

```
IconButton(
    icon: Icon(Icons.*exit_to_app*),
    onPressed: () async {
      SharedPreferences _prefs = await SharedPreferences.*getInstance*();
      _prefs.remove('id');
      _prefs.remove('token');
      try {
        var req = await _authAPI.destroySession(
            state.id, state.token); /* note state refers to the 
Customer from the cubit of our bloc builder
*/ if (req.statusCode == 202) {
          BlocProvider.*of*<CustomerCubit>(context).logout();
        } else {
          pushError(context);
        }
      } on Exception catch (e) {
        print(e);
      }
    })
```

感谢阅读，希望你学到了一些东西。要获得更多编程和应用程序开发教程，请务必查看我的 YouTube 频道，如果你想找乐子，请查看科里的角落播客！

科里的角落播客:[https://anchor.fm/coreys-corner](https://anchor.fm/coreys-corner)

学习飞镖和红宝石:【https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ】T2