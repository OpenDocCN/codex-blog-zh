# 如何在 Laravel 中创建自己的助手函数

> 原文：<https://medium.com/codex/how-to-create-your-own-helper-functions-in-laravel-d1515f950a45?source=collection_archive---------5----------------------->

![](img/fc302e8debdd257e0fb68de9be3a0ef1.png)

# 介绍

助手函数在您的 [Laravel](https://laravel.com) 项目中非常有用。它们有助于以一种干净简单的方式简化项目中的代码。Laravel 自带许多[辅助函数](https://laravel.com/docs/8.x/helpers#miscellaneous-method-list)，例如`dd()`、`abort()`和`session()`。但是随着您的项目开始增长，您可能会发现您想要添加自己的项目。

在本文中，我们将看看如何在我们的 Laravel 项目中创建和使用我们自己的定制 PHP 助手函数。

# 创建助手函数

要添加我们自己的助手函数，我们需要首先创建一个新的 PHP 文件来放置它们。因此，让我们在我们的`app`文件夹中创建一个`helpers.php`文件。顺便说一下，这个位置实际上取决于个人喜好。我见过的另一个放置文件的常见地方是在一个`app/Helpers/helpers.php`文件中。所以，选择一个你认为最适合你的目录是你自己的事。

现在，我们已经得到了我们的文件，我们可以添加我们的助手函数。作为本文的基本示例，我们将创建一个超级简单的函数，将分钟转换为小时。

让我们像这样将函数添加到我们的`helpers.php`文件中:

```
<?php

if (! function_exists('seconds_to_hours')) {
    function seconds_to_hours(int $seconds): float
    {
        return $seconds / 3600;
    }
}
```

正如你在上面的例子中看到的，函数本身非常简单。然而，您可能会注意到的一件事是，函数名是以蛇形书写的(例如，seconds_to_hours ),而不是像您通常在类中看到的方法名那样以骆驼形书写的(例如，secondsToHours)。您不一定要使用 snake case 来定义函数名，但是您会发现所有的 Laravel 辅助函数都是这样编写的。因此，我可能会建议使用这种格式，以便您可以遵循预期的典型标准和格式。不过这完全取决于你。

您可能已经注意到的另一件事是，我们将函数名包装在一个“if”语句中。这是为了防止我们意外地重新声明任何已经注册的同名帮助者。例如，假设我们正在使用一个已经注册了`seconds_to_hours()`函数的包，这将阻止我们用相同的名字注册我们自己的函数。为了解决这个问题，我们可以简单地改变我们自己函数的名字来避免任何冲突。

同样重要的是，在创建辅助函数时，要记住它们只能用作辅助函数。它们并不是真的用来执行任何业务逻辑，而是帮助你整理你的代码。当然，您可以向它们添加复杂的逻辑，但是如果这是您想要做的事情，我可能会建议您考虑一下代码是否更适合其他地方，例如服务类、操作类或特征。

# 注册助手功能

现在我们已经创建了我们的助手文件，我们需要注册它以便我们可以使用我们的新函数。要做到这一点，我们可以更新我们的`composer.json`文件，这样我们的文件就可以在每次请求时被加载，并且可供使用。这是可能的，因为 Laravel 在`public/index.php`文件中包含了 [Composer](https://getcomposer.org/) 类加载器。

在您的`composer.json`文件中，您应该有这样一个部分:

```
"autoload": {
    "psr-4": {
        "App\\": "app/",
        "Database\\Factories\\": "database/factories/",
        "Database\\Seeders\\": "database/seeders/"
    }
},
```

在本节中，我们只需添加以下几行，让 Composer 知道您想要加载文件:

```
"files": [
    "app/helpers.php"
],
```

您的`composer.json`文件的`autoload`部分现在应该是这样的:

```
"autoload": {
    "files": [
        "app/helpers.php"
    ],
    "psr-4": {
        "App\\": "app/",
        "Database\\Factories\\": "database/factories/",
        "Database\\Seeders\\": "database/seeders/"
    }
},
```

现在我们已经手动更新了`composer.json`文件，我们需要运行以下命令来转储我们的自动加载文件并创建一个新文件:

```
composer dump-autoload
```

# 使用助手功能

恭喜你！您的助手函数现在应该已经设置好了，可以使用了。要使用它，您只需使用:

```
seconds_to_hours(331);
```

因为它被注册为一个全局函数，这意味着你可以在很多地方使用它，比如你的控制器，服务类，甚至其他的帮助函数。我最喜欢助手功能的部分是能够在你的刀片视图中使用它们。例如，让我们想象一下，我们有一个包含一个与我们的新函数做同样事情的`secondsToHours()`方法的`TimeServiceClass`。如果我们要在我们的刀片视图中使用服务类，我们可能需要做这样的事情:

```
{{ \App\Services\TimeService::secondsToHours(331) }}
```

可以想象，如果在一个页面的多个地方使用这种方法，可能会让你的视图变得有些混乱。

# 更进一步

现在，我们已经看到了如何注册和使用助手，我们将看看如何才能更进一步。随着时间的推移，随着 Laravel 项目的增长，您可能会发现有大量的助手都在一个文件中。可以想象，这个文件可能开始看起来有点杂乱无章。因此，我们可能要考虑将我们的功能拆分到单独的文件中。

为了文章的方便，我们假设在我们的`app/helpers.php`文件中有很多帮助者；有些与金钱有关，有些与时间有关，有些与用户设置有关。我们可以从将这些函数分解成单独的文件开始，比如:`app/Helpers/money.php`、`app/Helpers/time.php`和`app/Helpers/settings.php`。这意味着我们现在可以删除我们的`app/helpers.php`文件，因为我们不再需要它了。

之后，我们可以用与之前类似的方式更新我们的`composer.json`文件，这样它现在可以加载我们的 3 个新文件:

```
"autoload": {
    "files": [
        "app/Helpers/money.php",
        "app/Helpers/settings.php",
        "app/Helpers/time.php",
    ],
    "psr-4": {
        "App\\": "app/",
        "Database\\Factories\\": "database/factories/",
        "Database\\Seeders\\": "database/seeders/"
    }
},
```

我们需要记住通过运行以下命令再次转储 Composer 自动加载文件:

```
composer dump-autoload
```

现在，您应该能够继续使用您的函数，并受益于它们被分割成逻辑上分离的文件。

# 结论

希望本文已经向您展示了如何为您的 Laravel 项目创建和注册您自己的 PHP 助手函数。请记住，不要使用它们来执行复杂的业务逻辑，而应该更多地将它们视为整理少量代码的一种方式。

如果你喜欢读这篇文章，并且觉得它很有用，请随意注册我的简讯，这样我每次发表新文章时你都会收到通知。

继续建造令人敬畏的东西！🚀

*最初发表于*[*https://ashallendesign.co.uk*](https://ashallendesign.co.uk/blog/how-to-create-your-own-helper-functions-in-laravel)*。*