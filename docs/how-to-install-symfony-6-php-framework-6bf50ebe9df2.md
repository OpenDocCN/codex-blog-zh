# 如何安装 Symfony 6 (PHP 框架)

> 原文：<https://medium.com/codex/how-to-install-symfony-6-php-framework-6bf50ebe9df2?source=collection_archive---------4----------------------->

## 安装 Symfony 6 和 Composer 的快速指南

![](img/ebf72772a28b874154713a08cf7cf57f.png)

[杰克·沃克](https://unsplash.com/@jakewalker?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

约翰尼刚刚看完报纸。

他读到一个人在去年赚了几百万美元。

他在县城找到了非常便宜的房子，并很快以更高的价格转卖。

Johnny 是一名初级开发人员，已经攒了一些钱。

所以决定创建一个程序来检查在线房地产门户网站，以找到价格最好的网站。

**他目前只知道简单的 PHP，所以一切都从零开始。**

一个月过去了，他只完成了认证和授权功能。

他意识到他为自己设定的任务需要太长时间才能成功完成，于是放弃了。

如果他学习一个 PHP 框架并让它来做所有艰难的事情，他会过得更好。

让我们投入进去吧！

# 什么是 PHP 框架？

PHP 框架是一个用于创建 web 应用程序的系统。

**它包括大量内置功能的组件和库。**

PHP 框架非常有用，因为它们已经包含了开箱即用的特性。

您不需要身份验证系统或路由系统。

需要连接到数据库吗？已经在那里了。

这将为你和 Johnny 节省大量时间，因为你可以专注于使你的网站独一无二的功能。

# 为什么要用 Symfony 6？

现在有很多框架。

Laravel，CodeIgniter，CakePHP 等。

另一个很棒的是 Symfony，具体来说就是 [Symfony 6](https://anastasionico.uk/blog/introduction-to-symfony-6-php-framework) 。

Symfony 6 有很多优点，比如它的速度和可用组件的数量。

此外，其庞大的社区使其成为互联网上问题最多的框架之一。

![](img/2716cbb996e74f836f35b12314952053.png)

由 [KOBU 机构](https://unsplash.com/@kobuagency?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 要求

让 Symfony 在毫无准备的情况下运行起来会很棒，对吧？

不幸的是，在你开始着手做之前，有些步骤需要准备好。

**你需要具备 PHP 的**[](https://anastasionico.uk/blog/php-basics)****基础知识。****

**这看起来很简单，建议起来很愚蠢，但却是事实。**

**我见过许多人在接触框架时对主要语言毫无头绪。**

**结果是，当他们需要改变工作或框架时，他们不能使用他们认为是语言一部分的助手。**

**在我写这篇文章的时候，Symfony 需要 PHP 8.1 才能工作。**

**它还需要一些 PHP 扩展，如 Ctype、iconv、PCRE、Session、SimpleXML 和 Tokenizer。**

**不要担心，因为大多数情况下，它们是用 PHP 自动安装的。**

**您需要能够使用框架的一个工具是 Composer。**

**你可以在我的[作曲家系列介绍](https://anastasionico.uk/blog/basics-composer-install-windows-mac-linux-and-components)里读到。**

**最后，建议安装内部 CLI。**

****它提供了几个你需要在本地运行 Symfony 的工具。****

**您可以使用命令检查是否满足了所有要求。**

**`symfony check:requirement`**

# **创建 Symfony 应用程序**

**Symfony 允许你创建不同类型的应用程序。**

****主要用于构建 web 应用。****

**您可以使用它来创建 API 或控制台应用程序。**

**您可以通过在命令中添加 webapp 标志来选择其中之一。**

**此外，在 Symfony 中创建新应用程序时，有两种不同的方法可供选择。**

**第一种是使用我上面展示的 Symfony 二进制。**

**第二种是直接使用 Composer。**

**以下是创建 web 应用程序时要使用的命令。**

```
#using composer 
composer create-project symfony/skeleton:"6.1.*" myProject 
cd myProject 
composer require webapp 

#or using the symfony binary 
symfony new myProject - version="6.1.*" --webapp
```

**如果您正在创建控制台应用程序或 API，您可以避免需要 webapp 组件或添加标志。**

```
#using composer 
composer create-project symfony/skeleton:"6.1.*" myProject 

#or using the symfony binary 
symfony new myProject --version="6.1.*"
```

**所有这些命令将创建一个`myProject`目录，并向其中添加初始框架代码。**

# **运行 Symfony 应用程序**

**有很多方法可以在你的电脑上运行一个 PHP 项目。**

**您可以使用 Docker 来运行它，或者在本地机器上设置一个 Nginx 或 Apache web 服务器。**

**不过最好的方法是使用 Symfony 提供的内置 web 服务器来运行您的项目。**

**它很容易上手，而且**提供了安全证书、HTTP2 支持**等等。**

**要启动您的项目，请在终端中键入以下命令。**

```
cd myProject symfony server:start
```

**如果没有错误信息，你可以访问你本地主机的 8000 端口，然后进入你网站的前端。**

**要停止服务器，只需在终端上按 CTRL+C。**

# **安装软件包**

**在这篇文章的前几部分，我强调了掌握 Composer 的重要性。**

****原因是 Symfony 大量使用包。****

**在框架中实现包时，通常需要更新和配置这些包。**

**有时需要手动添加额外的文件。**

****幸运的是，二进制文件提供了一个叫做 Flex 的工具。****

**Flex 通过自动化过程简化了这些外部包的安装。**

**一个明显的例子是日志包。**

**如果您只依赖 Composer 并需要 logger，终端会提示一条错误消息。**

**它会说记录器不存在。**

**如果启用了 Flex，它将提取包并添加使记录器工作所需的其他组件。**

****发生这种情况的原因是 Symfony 使用食谱。****

**菜谱是将软件包安装到应用程序中的指令。**

**这些配方存储在框架创建的名为 *symfony.lock* 的文件中。**

**如果您的项目在 GIT 存储库上，这个文件也必须提交。**

**如果你还不知道它是如何工作的，这里有一个 GIT 的[快速介绍。](https://anastasionico.uk/blog/git-for-php-developers)**

# **Symfony LTS 版本**

**Symfony 提供长期支持”(或简称 LTS)版本。**

****这些特别版本每隔几年发布一次。****

**你可以在 [Symfony releases](https://symfony.com/releases) 页面查看最后一个。**

**如果您想创建一个使用最新 LTS 的应用程序，您需要在命令中添加版本标志。**

```
symfony new my_project_directory --version=lts
```

**![](img/ac58babefaeffbf7232b1a667e81a664.png)**

**照片由[表面](https://unsplash.com/@surface?utm_source=medium&utm_medium=referral)在[不飞溅](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄**

# **结论**

**Symfony 是一个惊人的工具，可以用来创建各种 PHP 项目。**

**从 web 应用到 API 和微服务。**

**这个框架很容易开始，并且有了它的所有组件，你可以用它做任何事情。**

**你打算用它来构建的下一个项目是什么？**

**如果你将来有兴趣使用它，订阅时事通讯。**

**我正在写一系列的文章，将更深入地研究它和它的最佳用法。**

***原载于*[*https://anastasionico . uk*](https://anastasionico.uk/blog/how-to-install-symfony-6)*。***