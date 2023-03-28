# Flutter 用户认证第 2 部分:用 Cubit 存储用户

> 原文：<https://medium.com/codex/flutter-user-authentication-part-2-storing-users-with-the-cubit-6d60c537ce82?source=collection_archive---------1----------------------->

![](img/85a2cbef9fee8d6738aa7e7c115d0ea9.png)

迈克尔·泽兹奇在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在上一篇教程中，我教了你如何构建一个[客户模型和认证 API](/codex/flutter-user-authentication-part-1-models-and-api-acf33cf42f83)。为了让我们的客户对象在我们的小部件中可用，我们将使用 [flutter_bloc](https://pub.dev/packages/flutter_bloc) 包中的 Cubit(在继续之前，请确保安装这个包)。我们开始吧！

播客:[https://podcast . apple . com/us/podcast/coreys-corner/id 1479097455](https://podcasts.apple.com/us/podcast/coreys-corner/id1479097455)

学习编码:[https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ](https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ)

## 一肘是什么？

肘是集团架构的一个子集。**我认为 Cubits 是贯穿整个应用程序的一种状态形式**。Cubits 比 Blocs 更容易使用，因为它们依靠方法而不是流来处理状态变化，最终这意味着编写更少的代码。

## 创造我们的腕尺

关于身份验证，我们需要让用户登录和退出。在我们的 cubit 类中，我们将创建处理这些事件的方法，并最终改变我们的 Cubit 的状态。

```
class CustomerCubit extends Cubit<Customer>{ 
 CustomerCubit(Customer state) : super(state); 

  void login(Customer customer) => emit(customer);

  void logout() =>  emit(null);

}
```

在第一行中，我们创建了自己的 cubit 对象，状态类型为 Customer。第二行基本上是说，如果我们的 Cubit 没有存储用户，它将继承其父代的状态，也就是 null。

我们创建两个方法登录和注销。在每个方法中，我们通过调用 emit 来设置我们的 cubit 的状态，这个**本质上是 Cubit 类的 setState()的一个版本。任何从 Cubit 继承(扩展)的类都可以访问 emit。要让客户登录，我们传递一个客户实例，要让用户退出，我们传递 null。**

**在不到 5 行代码中，我们已经为在应用程序中存储客户对象打下了基础。**

## 使用腕尺

为了使我们的 Cubit 在我们的应用程序中可用，我们必须将我们的 Material 应用程序小部件包装在 BlocProvider 中:

```
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiBlocProvider(
      providers:  [
        BlocProvider<CustomerCubit>(
        create: (BuildContext context) => CustomerCubit(null)
        ),
      ],
      child: MaterialApp(
        title: 'Corey's Corner',
        theme: ThemeData(
          splashColor: Colors.orange,
          primarySwatch: Colors.orange,
          appBarTheme: AppBarTheme(elevation: 16.0),
        ),
        home: MyHomePage(),
      ),
    );
  }
}
```

我喜欢从 MultiBlocProvider 开始，以防以后需要添加更多的 Blocs 或 Cubits。

我们基本上只是告诉我们的提供者它将寻找什么类型的类，并告诉它初始状态应该是什么，在这种情况下应该是 null，因为没有用户初始登录。

## 在我们的 API 调用之后使用 Cubit

在我们的 API 请求之后，我们将需要调用我们之前构建的登录方法，查看下面的代码:

```
if(req.statusCode == 202){
    var customer = Customer.fromReqBody(req.body);
    BlocProvider.of<CustomerCubit>(context).login(customer); 
// a lot more stuff
}
```

假设我们从后端请求中获得了正确的响应代码，我们将访问我们想要使用的 cubit 的提供者，并使用我们从解码的 JSON 响应中创建的客户作为参数调用 login 方法。恭喜你刚刚成功地使用了腕尺！

## 使用 BlocBuilder 小部件

我们将根据 Cubit 发出的状态来构建小部件。例如，假设我们有一个带有按钮的导航条。最终，我们的应用程序布局将取决于 cubit 的状态。

```
class MyHomePage extends StatelessWidget {
  const MyHomePage({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    AuthAPI _authAPI = AuthAPI();
    return BlocBuilder<CustomerCubit, User>(
      buildWhen: (previous, current) => previous != current,
        builder: (BuildContext context, Customer state){
            return Scaffold(
                   appBar: AppBar(
                    title: Text("Corey\'s Corner"),
            leading: state == null ?
            IconButton(icon: Icon(Icons.login),
              onPressed: () {
                Navigator.push(context,
                    MaterialPageRoute(builder: (context) =>     Auth()));
              },
            ) : null, 
//more stuff
```

在我们的 widgets build 方法中，我们返回了一个 bloc builder，每当我们的状态改变时，它总是会重新构建。builder 参数被传递给一个函数，该函数将当前的 BuildContext 和我们的 cubit 的状态作为参数。

现在我们可以调用状态函数变量来访问 cubit 中的数据。在上面的代码中，我们使用三元运算符来构建一个 IconButton，如果我们的状态为 null(没有客户登录)，它将路由到登录页面。如果我们的状态不是 null(登录用户),我们显然不构建任何东西。

本教程到此为止！请继续关注下一个版本，因为我们将使用[共享首选项包](https://pub.dev/packages/shared_preferences)来保持用户登录，即使我们的应用程序关闭。感谢您的阅读，请务必查看[第三部分。](https://c0reygardner63.medium.com/flutter-user-authentication-part-3-persistent-login-with-shared-preferences-d0ffb64e1745)

播客:[https://podcast . apple . com/us/podcast/coreys-corner/id 1479097455](https://podcasts.apple.com/us/podcast/coreys-corner/id1479097455)

学习编码:[https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ](https://www.youtube.com/channel/UCfd8A1xfzqk7veapUhe8hLQ)