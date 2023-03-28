# 作曲家简介 5 分钟

> 原文：<https://medium.com/codex/introduction-to-composer-installation-and-components-5c9b14df8c73?source=collection_archive---------0----------------------->

## 如何做更多的事情

![](img/66100ec55251966d005fa413fcf09a8b.png)

[莱昂·温特](https://unsplash.com/@fempreneurstyledstock?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

如果你曾经使用过 PHP，你可能会遇到 Composer 这个词。

Composer 是一个工具，让世界各地的 PHP 开发人员声明他们的项目需要的库，并为您管理它们。

如果你想成为一名专业的 PHP 开发人员，这无疑是你必须学习的三大工具之一。

在本文中，我们将介绍它是什么以及如何使用它的基本知识。

开始吧！

开发 Composer 的人首先要做的事情之一就是指定它不是一个包管理器

即使这个工具处理包(或库)，它也是根据它们所习惯的项目来管理它们的。

您可以将 Composer 更多地视为一个依赖管理器。

Composer 将它们安装在您的项目中的一个目录下，这些包将只在该项目上工作。

(它也支持全球项目，但这是另外一个话题)。

# 要求和安装

成为 PHP 的依赖管理器意味着你的系统中需要有 PHP。

我知道这令人震惊。

它工作所需的 PHP 最低版本是 5.3.2。

我真的希望没有人在读它的时候还在运行低于 7 的 PHP 版本。

如果是这样，请停止阅读，去更新你的 PHP。

你回来了？凉爽的

让我们看看如何在您的机器上安装 Composer。

Composer 是多平台的，开发者试图让它尽可能的工作

目前，事实上，你可以在 Windows、Linux 和 macOS 上安装它。

# 在 Linux、Unix 或 macOS 上安装 Composer

Composer 有一个安装程序，您可以从命令行使用。

如果您正在使用一个类似 Unix 的系统，这可能意味着您可以轻松地使用它。

有两种方法可以安装这个工具，

*   **本地**在您的项目中
*   **全局**作为一个可执行程序存在于整个系统中

要在本地安装 Composer，您必须从您的目录中运行安装程序，查看[下载页面](https://getcomposer.org/download/)。

幕后将会发生的是，安装程序将**检查一些 PHP 设置并将名为 composer.phar 的文件下载到您的目录中。**

这个文件的格式是 PHP 档案，

要运行 Composer，你只需要运行 PHP composer.phar。

您也可以使用下面的命令并用标志`*— install-bin*`指定目录

这里有一个例子:

`php composer-setup.php --install-dir=bin --filename=composer`

如果您想全局安装 Composer，那么您必须将 phar 文件放在您的路径中的一个目录中。

运行安装程序后，您可以将文件移动到`local/bin`目录。

下面是要使用的命令:

`mv composer.phar /usr/local/bin/composer`

这样做之后，你可以简单地运行`composer`而不是使用命令`php composer.phar`

## Windows 操作系统

在 window 机器上安装 Composer 甚至更简单，

你需要做的只是下载并执行 [composer-setup 文件](https://getcomposer.org/Composer-Setup.exe)。

该可执行文件将更新路径本身，因此您只需在终端上运行 composer 即可。

# 基本用法

为了在你的项目中使用 Composer，你必须要有一个 JSON 文件。

这个文件将详细描述所有需要的依赖项，它还包含其他有用的元数据。

composer.json 中需要的最重要的东西是必需的键。

在官方文档中，他们使用[独白包](https://github.com/Seldaek/monolog)。

它所做的是将日志发送到文件、套接字、收件箱、数据库和各种 web 服务。

所需的键接受一个对象(由其名称表示)和版本。

```
{ "require": { "monolog/monolog": "1.0.*" }}
```

**Composer 所做的是检查正确的文件，这些文件不仅匹配包的名称，还匹配版本。**

**包名**由两部分组成，厂商名称和项目名称，在上面的独白中两者是相同的。

我们需要两者的原因是两个供应商可能以相同的方式调用他们的包，并且指定供应商的名称可以避免冲突错误。

简单对吗？

关于版本的价值，规范稍微复杂一点。

在上面的例子中，需要的是大于或等于 1.0 且小于 1.1 的 monolog 包的任何版本(注意*通配符)。

您还可以指定标签、分支和所需的最低稳定性，Composer 的人实际上创建了一个完整的关于[版本和约束](https://getcomposer.org/doc/articles/versions.md)的博客，您可以阅读。

一旦您在 JSON 文件**中指定了项目所需的包，您只需运行** `**composer install**` **命令，这些文件就会神奇地出现在项目目录中的 vendor 文件夹中。**

在本例中，您将有一个文件夹`vendor/monolog/monolog/`，如果 monolog 有自己的依赖项，它们也会列在 vendor 文件夹中。

一旦安装完成，就会创建一个包含所有依赖项及其项目版本的`composer.lock`文件。

将`composer.lock`提交给项目的回购协议是一个很好的做法，这样其他从事该项目的 PHP 开发人员就被锁定了，必须使用相同的版本。

这一点很重要！

原因是，如果在项目已经有 composer.log 的情况下用新版本更新 JSON 文件，然后运行 composer install 命令，那么两个文件之间的包版本将会不同。

这是由设计决定的，您和您的合作者希望维护所有包的相似版本，而不能更新版本以确保您的项目不会因为一个或多个依赖项的更改而中断。

如何更新依赖关系？

您知道在您的存储库中拥有`composer.lock`文件会阻止您的依赖项的任何新版本被更新。

**如果您确实想更新它们，一个解决方案是删除锁文件并再次运行 composer install 命令。**

这是可行的，但是你可以想象这不是处理这个问题的最好方法。

更好的方法是使用命令`composer update`。

这将获取 composer.json 文件中所需的依赖项的最新匹配版本，并用新版本更新锁文件。

如果需要，还可以通过指定一个，

需要举例吗？

这将起作用:

`composer update monolog/monolog`

# 包装和自动装载

网上有一些 Composer 库，这些是软件包源，可以描述为一个网站，你可以从那里获得软件包。

最流行的，也是我建议你使用的是 [Packagist](https://packagist.org/) 。

![](img/6a4478fb77a6b4046b3703baff5fc93d.png)

如果您有一个使用 Composer 的开源项目，强烈建议在 Packagist 上发布您的包。

尽管这不是必需的或强制性的，但在那里发布一个库将增加被发现的可能性，从而被其他开发人员采用。

Composer 的一个不常见但非常有趣的地方是它有平台包。

**这些是安装在系统上但 Composer 无法安装的功能的虚拟包。**

要获得这些软件包的完整列表，您必须在安装 Composer 后键入以下命令

`composer show --platform`

最后，

现在您已经安装了 Composer，并且已经需要了您喜欢的组件，您可能还想在您的项目中使用它们。

为此，通常的做法是利用`vendor/autoload.php`文件。

Composer 会自动生成这个文件，我们所能做的就是包含这个文件并使用所需的类。

下面是一个自动加载实现的代码片段

```
require __DIR__ . '/vendor/autoload.php';$log = new Monolog\Logger('name');$log->pushHandler(new Monolog\Handler\StreamHandler('app.log', Monolog\Logger::WARNING));$log->addWarning('Foo');
```

也可以将我们自己的代码添加到自动加载程序中。

我们可以通过更新 *composer.json.* 的*自动加载*部分来做到这一点

```
{
   "autoload": {
       "psr-4": {"Acme\Foo": "src/Foo.php"}
    } 
}
```

如你所见，它将注册一个 [PSR-4](http://www.php-fig.org/psr/psr-4/) 自动加载器

此外，它需要一个带有键和值的对象，键是类的名称空间，而值是文件名。

更新之后，您需要重新生成自动加载文件

我们将使用命令来完成此操作

`composer dump-autoload`

或者

`composer dumpautoload`

值得注意的是，Composer 也支持 PSR-0、类别映射和文件自动加载。有关更多信息，请参见自动加载参考。

前往[自动加载页面](https://getcomposer.org/doc/04-schema.md#autoload)了解更多信息。

# 图书馆

您应该了解的是，Composer 将每个具有`composer.json`文件的项目都视为一个包。

如果您的应用程序有 composer.json 包，

如果您需要 JSON 文件中的某个包，那么您的应用程序就是一个需要其他包的包。

唯一的区别是，目前你的项目(也就是你的包)没有名字。

让我们来解决这个问题！

添加名称的方法是在 JSON 文件中指定键名，然后添加供应商的名称和包的名称。

```
{ "name": "acme/hello-world", "require": { "monolog/monolog": "1.0.*" }}
```

通常，当你为一个项目编码时，你会创建不同的版本，

在大多数情况下，Composer 能够从您的版本控制系统中提取版本，并且您不需要在 JSON 文件中指定它。

但是，如果您自己维护没有 VCS 的项目，您必须通过在 composer.json 文件中添加版本值来明确指定版本。

```
{
    "version": "1.0.0"
}
```

很可能您最喜欢的 VCS 也支持标记和分支特性。

它的工作方式是 Composer 检查您的标签和分支，并将它们放入一个选项列表中，然后根据您提供的版本约束进行匹配。

官方手册的[版本和约束页面](https://getcomposer.org/doc/articles/versions.md)中有更多相关信息。

# 发布您的项目

如果您有一个包含 composer.json 文件的存储库，那么就可以安装这个库了。

让我们创建一个新项目(比如说一个博客)并在上面安装`acme/hello-world`。

```
// acme/blogProject/composer.json{
    "require": {
        "acme/hello-world": "dev-master"
    }
}
```

如您所见，因为我们不想发布这个项目，所以不需要这个名字。

我们需要的是描述在哪里可以找到 hello-world 依赖项。

我们可以通过给对象添加`repositories` 键来做到这一点。

```
{ "repositories": [        { "type": "vcs", "url": "[https://github.com/acme/hello-world](https://github.com/acme/hello-world)" } ], "require": { "acme/hello-world": "dev-master" }}
```

要使用 hello-world 和 monolog，您只需使用以下命令

`composer install`

你有没有注意到我们没有为 monolog 指定一个包存储库？

**原因是 monolog 是在 Packagist** 上发布的，在那个包库中发布的任何东西都可以通过 Composer 自动获得

由于 Monolog 在 Packagist 上，我们可以依赖它，而不必指定任何额外的存储库。

如果您想通过 Packagist 共享`acme/hello-world`，您只需访问 [Packagist](https://packagist.org/) 并点击“提交”按钮

# 结论

我怎么强调都不为过，

如果你梦想成为一名 PHP 开发人员，Composer 是最重要的工具之一。

从框架到自动化测试工具，您需要的一切都可以通过 Composer 获得。

很好地掌握它的功能和工作原理是非常重要的。

**在本文中，您学习了 Composer 的基础知识。**

你现在知道它是如何工作的，最重要的是它是做什么的。

**现在，您应该能够将软件包安装到您的存储库中，并将您的 how 软件包发布到 VCS 和打包程序中。**

在下面的文章中，我们将深入研究这个命令和 composer.json 模式，所以如果您想在新文章发布时得到通知，请订阅时事通讯。

同时，既然你已经知道了如何分享你的 PHP 包，那么温习一下如何编写令人惊叹的 PHP 可能是个好主意，你可以通过阅读 PHP 基础知识来学习如何做。

否则，学习如何使用[领域驱动设计](https://anastasionico.uk/blog/domain-driven-design-quickest-the-basics)开发复杂的应用程序可能会让你感兴趣。