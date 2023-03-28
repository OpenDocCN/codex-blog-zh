# 免费托管。净 6 个项目

> 原文：<https://medium.com/codex/free-hosting-for-net-6-projects-f65f76f2b973?source=collection_archive---------6----------------------->

## 让项目走向世界的快速、简单和免费的方法

![](img/d0075f17b82f9083fcd3c0d2bb60bcb9.png)

# 更新【2022 年 11 月:

> 从**2022 年 11 月 28 日**，**免费 Heroku Dynos** ，**免费 Heroku Postgres** ，Redis 免费 Heroku 数据**将不再可用**。

😢

俗话说，“天下没有免费的午餐”。然而，看起来，可能只是有一种免费的小吃，这正是 Heroku 的免费等级，如果它是一顿饭的话。由于完全免费且非常容易安装，它是向全世界展示您的小爱好项目或快速制作概念证明的完美选择。

## 先决条件

在我们开始之前，您应该有以下内容:

*   [**Docker**](https://docs.docker.com/get-docker/)
*   [**一个英雄帐**](https://signup.heroku.com/)
*   [**Heroku CLI**](https://devcenter.heroku.com/articles/heroku-cli)

所有这些设置都不应该超过 5-10 分钟。下载并安装了 Docker 和 Heroku CLI 并创建了一个 Heroku 帐户后，您就差不多准备好了。我想你已经有工作了。NET 6 应用程序，但如果你没有，这里有一个来自微软的 5 分钟 Hello World 教程。

## 创建 Dockerfile 文件

你可能已经猜到了，我们将使用 **Docker** 将我们的应用程序部署到 Heroku。如果你不熟悉 Docker，简而言之，它是一个**容器工具**，使部署变得更加容易。它被广泛使用并且非常有用。如果你想知道更多，你可以在这里阅读。

导航到项目的根目录，创建一个名为“Dockerfile”的文件。它应该是这样的:

。用于部署到 Heroku 的. NET 6.0 Dockerfile 文件

Dockerfile 的第一步(构建)使用官方的**。net 6.0 sdk** 镜像为基础构建 app 并保存在“out”文件夹中。

第二步(运行)使用官方**。net 6.0 运行时**镜像为基础，复制以前构建的文件并运行编译后的。net 6.0 dll。很明显，你应该用你的项目的名字替换“App”。

注意最后一行。在运行应用程序之前，我们将 **ASPNETCORE_URLS** env 变量设置为[http://*:**$ PORT**。这使得框架绑定到提供的 url。](http://*:$PORT)

Heroku 设置了 **$PORT** env 变量，并为我们提供了托管应用程序的实例的公开端口。

## 部署到 Heroku

现在我们已经准备好了 Docker 文件，您可以在项目的根目录(Docker 文件所在的位置)打开一个 shell。

首先，你需要登录 Heroku。为此，执行以下命令，这将打开浏览器并允许您登录。

```
heroku login
```

如果你遇到任何问题，或者，你可以使用“heroku 登录-i”。登录后，执行以下命令:

```
heroku create myapp --region eu
heroku container:login
heroku container:push -a myapp web
heroku container:release -a myapp web
```

首先，我们创建 heroku 应用程序。请注意，应用程序的名称必须是唯一的和可用的，因此您将需要使用除“myapp”之外的名称。关于**地区**标志，可以在**【欧盟】****【美国】**中选择。

然后，我们登录容器注册表，**使用我们创建的 Docker 文件构建**Docker 容器，**将**它推送到 Heroku，最后，**部署**它。

如果一切顺利，你的应用程序应该已经启动并运行了。您可以使用以下命令在浏览器中打开它:

```
heroku open -a myapp
```

## 添加数据库

你的应用可能需要一个数据库。幸运的是，使用 Heroku，添加数据库并将其链接到您的应用程序非常容易。有许多免费选项可供选择。在本教程中，我们将使用 **Heroku Postgres 爱好发展**计划。要将其添加到您的 Heroku 应用程序，请运行以下命令:

```
heroku addons:create heroku-postgresql:hobby-dev -a myapp
```

当您添加 Postgres 附加组件时，连接的 Heroku 实例将有一个名为 **DATABASE_URL** 的预设环境变量，其中包含访问数据库的 URL。看起来是这样的:

```
postgres://{user}:{password}@{hostname}:{port}/{database-name}
```

对于您的应用程序，您可能需要一个标准的 Postgres 连接字符串。我们可以使用这个漂亮的方法将 url 解析成一个连接字符串:

如果您使用的是实体框架，您可能会有一个如下所示的扩展方法:

您可以在配置服务时调用它，如下所示:

```
builder.Services.SetupAndAddDbContext(builder.Configuration);
```

# 结论

我已经向你展示了如何使用 Heroku 轻松、快速、零成本地部署你的应用和数据库。

显然，免费的 heroku 计划所能处理的有性能限制，包括“冷启动”,但我相信对于一个完全免费、易于设置的服务来说，这只是一个小小的不便。显然，它不是为生产应用程序设计的。

你遇到什么问题了吗？你对 Heroku 有什么想法吗？让我知道你的想法。