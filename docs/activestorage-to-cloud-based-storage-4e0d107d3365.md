# 动态存储到基于云的存储

> 原文：<https://medium.com/codex/activestorage-to-cloud-based-storage-4e0d107d3365?source=collection_archive---------8----------------------->

## [法典](http://medium.com/codex)

## 用 AWS 保存 Heroku 应用程序数据

![](img/b2406d9e1b2ef5b60dd042c8d34a9f4c.png)

照片由来自[佩克斯](https://www.pexels.com/photo/white-clouds-2114014/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)的[玛格达·埃勒斯](https://www.pexels.com/@magda-ehlers-pexels?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)拍摄

## 飘忽无常；很快结束（或忘记）的；短暂的

有没有在 Heroku 上启动一个应用程序，第二天打开时发现它无法运行，你的数据不见了？搞什么鬼？！

问题是 Heroku 的驱动器是“短暂的”——这意味着你可以向它写入文件，但当应用程序重新启动时，它们不会被持久化。如果您使用的是 Ruby on Rails 的活动存储，信息将暂时通过本地存储访问，但会在 24 小时后消失。

那么，现在怎么办？

## 进入基于云的解决方案

您可以利用基于云的存储来保存您的数据，而不是将上传内容存储到 Heroku 的硬盘上。你可以使用很多服务，但是 Heroku 的文档建议使用亚马逊的 S3。

S3 是为存储对象而构建的，重点是安全性和可伸缩性。它也可以自由使用，但有一些限制。为一个小应用程序存储数据可能不会花费一毛钱。

它的工作方式是把你的数据上传到一个“桶”里。如果需要将一些数据分开，可以有多个存储桶。一旦你有了这样的设置，你需要对你的应用程序做一些小的修改来连接它。

## 初始设置

在我们开始之前，您需要确保您已经在 Heroku 上成功启动了您的应用程序，并验证了您的数据在本地可用。如果你还没有这样做，看看 Heroku 简单易懂的指南[这里](https://devcenter.heroku.com/start)。

提示:定义一个允许您从生产和开发 URL 切换的动态 API 变量，可以节省您的时间，并在测试本地存储和 S3 存储时避免可能的混淆。例如，如果您使用 React 前端，您可以创建一个标记为 API.js 的文件，并添加以下内容:

```
const PROD_URL = 'https://your-app.herokuapp.com'const DEV_URL = 'https://localhost:3000'const API = process.env.NODE_ENV === 'development' ? DEV_URL : PROD_URL;export default API;
```

## AWS 入门

将数据迁移到 AWS 的第一步是[创建一个账户](https://portal.aws.amazon.com/billing/signup#/start)。这是不言自明的。导航到 AWS S3，在那里您将创建您的第一个存储桶。

单击橙色的“创建存储桶”按钮后，输入适合您的应用程序和/或您计划存储的数据的存储桶名称。请密切关注您选择的 AWS 地区。稍后您将需要它以及您的秘密访问密钥和秘密访问 id。你可以暂时不去管其他的设置。

## 将 AWS S3 数据链接到应用程序

恭喜你！您已经准备好将这个基于云的存储链接到您的应用程序。我们需要修改几个文件来使它正常运行。

为了保证数据的安全，您需要设置一个包含 AWS 秘密的隐藏变量。如果您在创建 bucket 时没有保存密钥和 id，您可以创建一个新的访问密钥来使用，方法是登录到您的 AWS 帐户，单击右上角的用户名，然后选择 My Security Credentials。

一旦你有了，访问你的 config/storage.yml 文件。你可以在这里找到下一步的提示。

```
#Use rails credentials:edit to set the AWS secrets (as aws:access_key_id|secret_access_key)
```

首先遵循这些说明，然后在下面包含以下代码:

```
amazon:service: S3access_key_id: <%= Rails.application.credentials.dig(:aws, :access_key_id) %>secret_access_key: <%= Rails.application.credentials.dig(:aws, :secret_access_key) %>region: enter-region-herebucket: 'enter-bucket-name-here'
```

现在确保 config/environments/production . Rb 包含:

```
config.active_storage.service = :amazon
```

在您的 gem 文件中，您需要添加:

```
gem 'aws-sdk-s3', require: false
```

之后不要忘记捆绑安装！

## 可能的障碍

如果您遇到 CORS 错误，可能是因为您正在发送不安全的文件，或者您没有正确设置 bucket 的读/写权限。你可以在这里阅读更多关于这个问题以及如何解决它的信息[。](https://docs.aws.amazon.com/config/latest/developerguide/s3-bucket-policy.html)

另一个需要注意的重要事情是，您可能无法使用 Rails' :includes 方法。如果这给你带来了问题，简单地使用序列化程序代替。这个[博客](https://itnext.io/a-quickstart-guide-to-using-serializer-with-your-ruby-on-rails-api-d5052dea52c5)是设置序列化程序的一个很好的资源，如果你还没有尝试过的话。它改变了游戏规则。

## 最后一点:耐心点

从 AWS 到后端，再到前端，再到 Heroku，都有很多变量在起作用。在这里慢慢来。每一步都用调试工具测试您的输入和输出。一次做出假设或太多的改变会导致以后很难追踪到的错误。享受这个过程。