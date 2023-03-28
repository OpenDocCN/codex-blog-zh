# 如何使用 Deno、Opine 和 DenoDb 用 TypeScript 构建 REST API

> 原文：<https://medium.com/codex/how-to-build-a-rest-api-with-typescript-using-deno-opine-and-denodb-82e1411298a?source=collection_archive---------21----------------------->

![](img/027bd97a7289ee90c769cbe01208d09a.png)

这些天我开始学习 TypeScript，我对 Deno 感兴趣，“Deno 是一个简单、现代、安全的 JavaScript 和 Typescript 运行时，它使用 V8 并内置于 Rust 中。”。我很好奇如何用它构建一个 REST API。我真正喜欢的框架是 Opine，“从 ExpressJS 移植而来的快速、极简的 web 框架。”根据它的文件。

在本教程中，我们将使用 Deno 构建一个 REST API，Opine 作为 web 框架，DenoDB 作为 ORM。

我们将开始定义我们的模型。我将使用 DenoDB 教程中的相同模型。

**要求:**

*   Deno 安装在你的机器上(你可以从[这里](https://deno.land/)安装)。
*   了解面向对象的基本概念(类、方法、实例)。

```
-----OpineApi
       |------model
       |      |-----Flight.ts
       |------router
       |      |-----Routers.ts
       |------app.ts
       |------db.ts
```

我们在 API 目录中创建一个模型文件夹，并创建一个模型文件。

**Flight.ts**

在 Flight.ts 中，我们需要从 DenoDB 导入数据类型和模型类，然后创建一个 Flight 类，它继承了模型类的属性和方法。然后，我们将表命名为“flights ”,并将时间戳标记为 true，这将显示我们何时创建行以及何时更新行。

然后，我们定义我们的模型的字段及其数据类型，在这种情况下:id、出发、目的地和飞行持续时间，如果我们不指定飞行持续时间，它将默认显示 2.5。

之后，我们在 API 目录中创建一个文件，我们将在这个目录中连接数据库。我打算使用 SQLite，但是 DenoDB 支持 PostgreSQL、MySQL、MariaDB 和 MongoDB。

**db.ts**

我们需要从 Deno Db 导入数据库和 SQLite3Connector，从模型文件导入 Flight 类。

我们创建一个不可变变量来存储 SQLite 连接器的实例，并指定 SQL 文件的路径。然后我们创建一个不可变的变量来存储一个数据库类的实例，连接我们的数据库，之后，我们将它链接到模型。

现在，我们在 API 目录中创建一个路由器文件夹，并创建一个文件来对路由器进行编程。

**路由器. ts**

我们的第一个路由器是用来获取数据库中的所有航班。我们需要创建一个 opine 实例，然后使用 get 方法。我们将一个不可变的变量声明为 flights，以存储我们可以通过方法 all()访问的所有航班，该方法来自 Flight 类继承的模型类。然后它发送一个 JSON 作为响应。如果有错误，它将发送一条错误消息。

该路由器将通过其“id”获得航班。我们使用飞行课程中的“where”方法。 [req.params.id](http://req.params.id) '是 opine 中从路径中检索 id 参数的方法。如果路径中没有指定 id 的航班，它将发送消息“未找到航班”。如果有航班，它将把请求体作为 JSON 发送。

在这个路由器中，我们使用 create 方法来发布请求正文。如果发布成功，它将发送状态代码“200”和作为响应发布的正文。

为了进行更新，我们使用了与预订航班几乎相同的代码。不同的是，我们使用的是 update 方法。

同样的事情也适用于从数据库中删除行。我们使用 Flight 类中的 delete 方法。这些方法来自 DenoDb。代码删除其 id 在路径中指定的行。

**应用程序**

这个文件是服务器执行的地方。我们创建一个 opine 实例，使用“listen”方法并传递 API 将要使用的端口。' db.sync()'用于同步数据库。

为了运行服务器，我们需要在我们的终端中编写这个，因为 Deno 在默认情况下是安全的:

```
deno run --allow-net --allow-write --allow-read app.ts
```

如果你对其他包有什么推荐，如何提高我的代码，我的英语，什么都可以；请留言或通过 [Twitter](https://twitter.com/Carlos_marcv) 或 [LinkedIn](https://www.linkedin.com/in/carlos-marcano-a2135a134/) 联系我。

源代码是[这里](https://github.com/carlosm27/opine_api)

参考资料:

*   [Deno 手动](https://deno.land/manual)
*   [意见文件](https://github.com/cmorten/opine/blob/main/.github/API/api.md)
*   [DenoDb 文档](https://eveningkid.com/denodb-docs/docs/getting-started)

*最初发布于*[*https://Carlos mv . hash node . dev*](https://carlosmv.hashnode.dev/how-to-build-a-rest-api-with-typescript-using-deno-opine-and-denodb)*。*