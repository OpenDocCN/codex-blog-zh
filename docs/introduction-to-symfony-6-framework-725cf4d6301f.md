# Symfony 6 框架简介

> 原文：<https://medium.com/codex/introduction-to-symfony-6-framework-725cf4d6301f?source=collection_archive---------6----------------------->

## 开始制作网站所需要知道的一切

![](img/a3d8107b4bb1f73a5174e1ad4148a235.png)

哈尔·盖特伍德在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我只用了 3 天就建立了一个完整的网站。

我的秘密？

我用过一个 **PHP web 框架！**

在这篇文章中，我将一步一步地向你展示你需要知道的复制我所做的事情。

# 什么是 Symfony？

让我们从最基本的开始。

Symfony 是一个 PHP 框架。

您可以出于多种原因使用它。

*   提高您的生产力
*   加快 web 应用的开发速度

它通过实现许多可重用的组件来做到这一点。

Symfony 是目前 PHP 市场上最流行的框架之一。

目前，大约有 [110，751 个网站是用 Symfony](https://trends.builtwith.com/framework/symfony-PHP-Framework) 制作的，相比之下，有 325，287 个是用 Yii 框架编码的，1，630，401 个是用 Laravel 的一个版本制作的，还有 831，597 个是用 Python 制作的。

为了能够学习它，你需要一些基础知识

它们是:

*   HTML5
*   CSS3
*   一点 JavaScript 不会有什么坏处
*   当然是 PHP
*   面向对象编程
*   什么是 MVC 模式的基本知识

# 使用 PHP 框架的优势

在框架出现之前，创建 PHP 应用程序的唯一方法是使用普通的 PHP。

![](img/72e61234f5791e6bb6e6083ee5e67587.png)

照片由[卢](https://unsplash.com/@riku?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

现在有很多框架。

创建 web 应用程序要容易得多。

**Symfony 提供了大量现成的工具。**

除了这些工具，还有许多组件可供您使用。

这些可以是内置的或第三方的组件，您可以将它们插入到应用程序中。

HTTP 请求、服务容器、模型绑定、路由器、配置文件等。

创建以上所有系统需要时间。

在 Symfony 中，您可以多次重用它们。

> "学习一门新编程语言的唯一方法是用它来写程序."—丹尼斯·里奇

# 编码标准

Symfony 有一些编码标准，您可以遵循这些标准来使框架以最佳状态工作。

遵循这些模式的另一个原因是它们可以提高代码的可读性。

如果你使用和其他开发者一样的标准，他们只需要几秒钟就能理解文件系统。

以下是其中几个例子:

*   在类中，按以下顺序声明方法:公共、受保护、私有
*   在类中，在方法之前声明属性
*   每个文件只有一个类
*   始终使用大括号来控制显示结构体
*   在二元运算符周围添加一个空格
*   在逗号分隔符后添加一个空格

这里是 Symfony 官方文档中的一页，你可以在那里找到所有这些标准。

[](https://symfony.com/doc/current/contributing/code/standards.html) [## 编码标准(Symfony 文档)

### 编辑此页面 Symfony 代码是由世界各地成千上万的开发者贡献的。让每一段代码…

symfony.com](https://symfony.com/doc/current/contributing/code/standards.html) 

Symfony 强调了这些编码标准。

这是一个很好的经验法则，即使你用普通的 PHP 或其他框架编码。

# 命名规格

除了编码标准，遵循框架选择的命名约定也非常重要。

这有许多原因。

一个是代码可读性。

另一个原因是 Symfony 确实使用了很多反射 API 来控制类的系统。

![](img/765a5e56244356a05a0268b86de1ed11.png)

法比奥·桑塔尼耶洛·布鲁恩在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

用标准的方式编写代码有助于保持代码的可用性。

这里有一些你需要遵守的规则:

*   对变量、函数和方法参数使用 camel 大小写
*   对配置参数使用 snake case(也是分支模板变量)
*   类、接口、特征和枚举需要大写
*   接口、特征和异常需要加上它们是什么的后缀

这里有一些例子

骆驼的情况是这样的:

`$createdAt`、`public function getTimeCreation(){...}`、`getTimeCreation($currentTime)`。

这是蛇的情况:

`framework.csrf_protection`，`http_status_code`

上部驼色大写字母开头:

`ConsoleLogger`、`BlogController`、`CommentableInterface`

# 为什么要使用框架？

从前，开发人员负责编写应用程序的每个方面。

现在，这些日子已经过去了，许多功能都留给了框架。

同样，利用可重用的包也是值得的。

它们通常由大量开发人员开发，并且经过了良好的测试。

**框架的目标是在开发过程中提供帮助。**

它们提供标准和底层功能。

这意味着程序员可以专注于项目的实际范围。

此外，考虑对基本代码进行改进的开发人员的数量。

它增强了安全性并提供了大量支持。

> “有测试总比没有测试好！”Symfony 框架的创立者杨奇煜·普登西耶

“不是我”提到框架为你和你的老板节省时间和金钱。

如今没有必要重新发明轮子，自己做事情。

# symfony Vs Laravel Vs CodeIgniter Vs 其他

现在有很多 PHP 开发框架。

他们每个人都有自己的优点和缺点，但他们的主要目标是相同的。

提供一个结构，使开发人员的工作更容易。

Symfony 是其中最著名的。

在我写这篇文章的时候，最新的版本是 Symfony 6。

**Symfony 非常好，因为它提供了大量的独立组件，你可以将它们插入到你的项目中。**

![](img/a2e03c51465bdb614529fe6daf3d0690.png)

照片由 [Linus Mimietz](https://unsplash.com/@linusmimietz?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

学习 Symfony 的另一个优势是 Drupal，

Drupal 是互联网上最重要的内容管理系统之一。

它有非常相似的结构。

这意味着你可以快速制作博客、电子商务和其他类型的在线应用。

并且使用与编写 Symfony 应用程序相同的技能。

Laravel 是目前最流行的 PHP 开发框架。

它的优点是它很容易使用，并且提供了大量的功能。

此外，Laravel 社区很大，你总能找到解决问题的方法。

最大的问题是 Laravel 提供了大量的内置功能。

这些是资产，但是它们使系统变得更重，因此它们降低了应用程序的速度。

如果你想要一个更精简、更快的 PHP 框架，还有很多其他的选择。

> "真理只能在一个地方找到:代码."―罗伯特·马丁

CakePHP、Codeigniter 和 Yii 都是很好的选择，各有利弊。

选择它们的最好方法是获取一些信息，然后对它们进行测试。

一旦你在这里或那里做了一些实验，你就可以选择你最喜欢的一个。

# 结论

这是对什么是 PHP 框架以及为什么首先要使用它的快速介绍。

如果您是框架的新手，或者在接下来的文章中有使用框架的经验，我们将开始一段旅程。

事实上，在这个系列中，我将使用它重新创建我自己的网站。

因此，点击关注按钮，每当有新文章发布时，您就会收到通知。

还有，你对 PHP 框架最好奇的是什么？

*最初发布于*[*https://anastasionico . uk*](https://anastasionico.uk/blog/introduction-to-symfony-6-php-framework)*。*