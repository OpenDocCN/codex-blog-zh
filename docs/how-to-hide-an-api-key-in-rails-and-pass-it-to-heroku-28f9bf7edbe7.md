# 如何在 Rails 中隐藏一个 API 密钥并将其传递给 Heroku

> 原文：<https://medium.com/codex/how-to-hide-an-api-key-in-rails-and-pass-it-to-heroku-28f9bf7edbe7?source=collection_archive---------9----------------------->

![](img/03f283ef473d6a7bb7d824eea88ac6a5.png)

在这篇博客中，我们将看看如何在 Rails 中隐藏面向公众的 API 密钥，以及如何将它们传递给 Heroku 上部署的 Rails 应用程序。

使用外部 API 是创建有用且有趣的应用程序的好方法。对于许多高质量的外部 API，将需要一个授权密钥。甚至许多免费的 API 都需要一个密钥。许多 API 只会给你每月分配的 API 调用数量，否则，你将不得不付钱来获得更多。如果你将你的代码存储在任何类型的在线公共回购中，比如 GitHub，最重要的是你要隐藏你的 API 密匙，这样就没有人能从你的在线回购中窃取它并为自己所用。如果您的应用程序被部署到 Heroku，您还需要将保存的密钥传递给 Heroku，以便您的项目进行正确的调用。让我们开始吧！

在本教程中，您需要做的第一件事就是获取 API 的密钥。API 应该有关于如何获得唯一密钥的文档。一旦你有了这个，想法就是把它保存为 Rails 中的一个环境变量。我们将使用包含 API 键的环境变量，而不是将 API 键直接插入 API 调用。这将允许我们在代码中对 API 密匙保密。

## Rails Gem Dotenv

下一步是打开您的 Rails 项目并将 [gem](https://github.com/bkeepers/dotenv) `'dotenv-rails'`安装到您的 gem 文件中。您可以通过手动将`gem 'dotenv-rails'`添加到您的 Gemfile 中，然后在终端中运行`$ bundle install`，或者您可以简单地在终端中运行`$ gem install dotenv`来实现这一点。

## 创建环境变量

安装完成后，我们可以通过在项目中创建一个名为`.env`的新文件，将我们唯一的 API 密匙作为环境变量传递。让我们为我们的键起一个名字，我们称之为`MY_API_KEY`，让我们假设我们的 API 键是`1234567`。在新的`.env` 文件中，我们可以像这样保存我们的 API 密钥:

```
MY_API_KEY=1234567 
```

这个变量可以在 Rails 应用程序中的任何地方使用，如下所示:

```
ENV[“MY_API_KEY”]
```

因此，要在我们的代码中的 API 调用中使用这个新变量，它可能看起来像这样:

```
key = ENV[“MY_API_KEY”]url = “https://api.rawg.io/api/games/7153?key=#{key}"
```

## 。gitignore

下一步超级重要。我们需要将`.env`文件添加到`.gitignore`文件中，以确保与我们唯一的 API 键相关的文件不会与我们的代码一起被推送到任何在线存储库中。

如果您希望其他人能够克隆您的 repo 并在他们自己的机器上运行您的项目，请确保在自述文件中包含文档，说明他们将需要自己用名为`MY_API_KEY`的环境变量创建一个新的`.env`文件，并将他们自己的密钥插入其中。

## 将 API 密钥传递给 Heroku

现在我们的密匙在本地被安全地使用，在我们的在线存储库中没有被窃取的危险，让我们看看如何将它放入我们在 Heroku 上部署的代码中。

先决条件:我假设您已经在 Heroku 上部署了一个 Rails 应用程序，并且对 Heroku CLI 有些熟悉。

因为我们的`.env`在我们的`.gitignore`文件中，所以当我们推送包含环境变量用法的新代码时，它不会被发送到 Heroku。所以我们代码中任何使用`ENV[“My_API_KEY”]`的地方都找不到那个变量的值。

为了解决这个问题，我们可以在部署的 Heroku repo 中创建一个[配置变量](https://devcenter.heroku.com/articles/config-vars)。

首先，登录你的 Heroku 账户。接下来，在命令行中输入以下内容:

```
$ heroku config:set MY_API_KEY=1234567
```

这应该将 API 键保存为一个配置变量。为了验证密钥是否正确保存，我们可以使用以下命令进行检查:

```
$ heroku config:get MY_API_KEY
```

我们应该会在终端看到`1234567`。

在 Heroku 上的代码中将使用 Config 变量，就像本地机器上的环境变量一样。所以给它们取相同的名字是非常重要的！

*如果您不想使用命令行方法，也可以从 Heroku 仪表板的* `Settings` *选项卡将配置变量传递给 Heroku。*

就是这样！让那些 API 密匙安全快乐的建造吧！