# 如何用 Java 发送短信

> 原文：<https://medium.com/codex/how-to-send-sms-messages-with-java-6b588b22fbdf?source=collection_archive---------23----------------------->

![](img/775a9cd59e3c1bebeba29f30ae99b5e1.png)

*我们使用 JDK 16、Gradle 7.1 和 Vonage Server SDK for Java v . 6 . 4 . 0*构建了这个例子

[Vonage SMS API](https://developer.vonage.com/messaging/sms/overview) 是一项允许你在世界任何地方发送和接收短信的服务。Vonage 提供了 REST APIs，但是使用我们为您编写的 Java SDK 要容易得多。

在本教程中，我们将介绍如何用 Java 发送短信！查看 GitHub 上的[源代码。](https://github.com/nexmo-community/send-sms-java/blob/main/src/main/java/getstarted/SendSMS.java)

# 先决条件

希望您已经对 Java 编程有了基本的了解——我们不打算进行任何高度复杂的编程，但它将帮助您入门和运行。除了对 Java 有基本的了解之外，您还需要在开发机器上安装以下软件:

*   [Java 开发套件(JDK)](https://www.oracle.com/java/technologies/javase-downloads.html)
*   [Gradle](https://gradle.org/) 用于构建您的项目

# Vonage API 帐户

要完成本教程，您将需要一个 [Vonage API 帐户](http://developer.nexmo.com/ed?c=blog_text&ct=2017-05-03-send-sms-messages-with-java-dr)。如果你还没有，你可以今天就[注册](http://developer.nexmo.com/ed?c=blog_text&ct=2017-05-03-send-sms-messages-with-java-dr)开始用免费的信用点数来建造。一旦你有了一个帐户，你可以在 [Vonage API 仪表板](http://developer.nexmo.com/ed?c=blog_text&ct=2017-05-03-send-sms-messages-with-java-dr)的顶部找到你的 API 密匙和 API 秘密。

本教程还使用了一个虚拟电话号码。要购买号码，请前往*号码* > *购买号码*并搜索符合您需求的号码。

![](img/941d1ebad7252085aa9c1e88f9dc18f8.png)

# 使用 Vonage Java SDK

首先，您需要设置您的 Gradle 项目并下载 Vonage Java SDK。

创建一个目录来包含您的项目。在这个目录中，运行`gradle init`。如果您以前没有使用过 Gradle，不要担心——我们不会做任何太复杂的事情！选择 *1: basic* 作为要生成的项目类型，选择 *1: Groovy* 作为构建脚本 DSL，并将您的项目命名为——或者按 Enter 键作为默认选项。

接下来，打开`build.gradle`文件，将内容更改如下:

这个命令将下载 Vonage Java SDK 并存储起来以备后用。如果你有源代码，它也会编译，但是你还没有写。让我们解决这个问题！

因为我们在 Gradle 构建文件中设置了`mainClass`，所以您需要在包`getstarted`中创建一个名为`SendSMS`的类。在生产代码中，您可能希望包类似于`com.mycoolcompany.smstool`，但是这不是生产代码，所以`getstarted`也可以。

Gradle 使用与 Maven 相同的目录结构，因此您需要在您的项目目录中创建以下目录结构:`src/main/java/getstarted`。

在 macOS 和 Linux 上，您可以通过运行以下命令来创建该路径:

所有这些只是导入 Vonage SDK 的必要部分，并创建一个方法来包含我们的代码。现在值得运行`gradle run`，它应该运行你的 main 方法。它还不会做任何事情，但这是我们开始激动人心的部分。

# 用 Java 发送短信

在您的`main`方法中加入以下内容:

同样，您需要用包含您购买的虚拟号码和您自己的手机号码的字符串来替换`VONAGE_BRAND_NAME`和`TO_NUMBER`。确保以 [E.164 格式](https://developer.vonage.com/voice/voice-api/guides/numbers)提供`TO_NUMBER`，例如 447401234567。
完成后，保存并再次运行`gradle run`。您应该会在屏幕上看到类似这样的内容:

`Message sent successfully.[com.vonage.client.sms.SmsSubmissionResponseMessage@f0f0675[to=447401234567,id=13000001CA6CCC59,status=OK,remainingBalance=27.16903818,messagePrice=0.03330000,network=23420,errorText=<null>,clientRef=<null>]]`

…您应该会收到一条短信！如果不起作用，请检查上面一行中的`ERR:`之后是否打印了某些内容，也许再等几秒钟消息就会出现。

> 注意:在某些国家(美国)，`VONAGE_BRAND_NAME`必须是您的 Vonage 虚拟号码之一。在其他国家(英国)，您可以自由选择一个字母数字字符串值——例如，您的品牌名称 AcmeInc。在[开发门户](https://developer.vonage.com/messaging/sms/guides/country-specific-features)上阅读特定国家的短信功能。

您刚刚学习了如何使用 Vonage 发送短信！

# 参考

*   [Vonage SMS API 引用](https://developer.vonage.com/api/sms?theme=dark)
*   [Vonage Java SDK](https://github.com/Vonage/vonage-java-sdk)
*   [特定国家短信功能](https://developer.vonage.com/messaging/sms/guides/country-specific-features)

*最初发布于*[*https://learn . vonage . com/blog/2017/05/03/send-SMS-messages-with-Java-dr*](https://learn.vonage.com/blog/2017/05/03/send-sms-messages-with-java-dr/?utm_source=twitter&utm_medium=organic&utm_campaign=social_media)