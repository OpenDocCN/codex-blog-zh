# 在 Sendbird 聊天中设计消息对象

> 原文：<https://medium.com/codex/designing-message-objects-in-sendbird-chat-def5251ed40b?source=collection_archive---------15----------------------->

![](img/3ba799bcd7127ddcd47173a8cafd2eb6.png)

仙鸟 2022

## 了解 Sendbird Chat 中视频文件、gif 和缩略图的技术注意事项

杰森·奥尔肖恩
解决方案工程师| [仙鸟](https://www.sendbird.com/)

*有关本教程材料的其他指导，请参见* [*文档*](https://sendbird.com/docs/chat) *。查看 Sendbird Chat 的* [*演示*](https://sendbird.com/demos/in-app-chat) *并访问我们的* [*网站*](https://sendbird.com/features/chat-messaging) *以了解更多关于 Sendbird Chat 的信息。*

> *成为第一个了解新教程、开发者相关聊天/电话发布以及其他重要更新的人，* [*注册*](https://get.sendbird.com/dev-newsletter-subscription.html) *我们的开发者简讯。*

# 介绍

有了 Sendbird，发送和接收信息变得很容易。(如果您还没有，请查看我们的 [**文档**](https://sendbird.com/docs/chat) 开始使用。)然而，设计消息对象并获得您想要的外观和感觉可能需要大量的工作。在本指南中，您可以了解到尽可能使用 Sendbird 消息对象的众多方法中的几种。

我们将介绍如何:

*   发送视频文件
*   发送 gif
*   创建缩略图

为了从本指南中受益，我们建议在实施本指南中介绍的内容之前，创建一个免费的 [**Sendbird 帐户**](https://dashboard.sendbird.com/auth/signup) 和 [**设置基本聊天功能**](https://sendbird.com/docs/chat) 。这会帮助你学得更快。

话虽如此，我们还是开始吧！

# 发送视频文件

首先，我们来了解一下发送视频文件的细节。Sendbird 的文件上传服务允许任何数据文件上传并存储在 Sendbird 中。包含文件细节的消息具有 Sendbird SDK 消息。类型等于文件，此后称为文件消息。文件消息可以存储指向内部和外部数据的 URL 链接。

以下是上传和处理 Android、iOS 或 Javascript 视频需要知道的内容。

## 文件上传大小限制

如果你直接上传文件到 Sendbird，每个文件有一个大小限制，这取决于你的 Sendbird 计划。如果您上传的文件超出了限制，您将会看到如下错误:

上传的最大文件大小为 25Mb。Code = 400111 。

请注意，如果您使用外部 URL(如 YouTube 或 Vimeo)发送文件信息，Sendbird 没有大小限制。

要查看您的计划允许什么，请访问您的 [**Sendbird 仪表板**](https://dashboard.sendbird.com/auth/signin) 。如果您需要更高的尺寸限制， [**请联系 Sendbird 销售团队**](https://sendbird.com/contact-sales) 。

## 拇指甲

如果您的应用程序使用 Sendbird Premium 缩略图生成工具，您可以选择为您的视频生成缩略图。自动生成的缩略图是小的静态图像，仅来自上传的视频。缩略图不会从外部链接的视频中创建。

## 适度

目前，Sendbird 的审核工具不适用于视频文件。

## 用户体验考虑因素

**Sendbird 文件消息的异步特性**

理解发送和接收文件消息的时间是不同的，这一点很重要。视频文件越大，发送包含实际文件数据的 Sendbird 文件消息所需的时间就越长。但是，当收到文件消息时，它的接收速度与普通用户消息相同，因为收到的文件消息包含指向该文件的 URL 链接，而不是实际的文件数据。

## 大型文件的 UX 实施注意事项

对于大文件上传，考虑在你的 UI 中提供一个进度指示器。随着多部分上传的进行，Sendbird 拥有从 0.0 到 1.0 递增的进度处理程序(这适用于 iOS、Android 和 Javascript)。上传完成后，进度指示器显示为 1.0。

此外，还要考虑服务器端的处理时间，比如缩略图生成。您可以隐藏文件上传指示器，并在等待文件消息回调时显示一个活动微调器。一旦 Sendbird 的服务器端操作完成，文件消息回调将返回一个成功的消息对象或一个错误消息。

以下是 Swift 中建议的操作顺序:

## 互联网连接中断

一旦调用了 sendFileMessage()，Sendbird 的 SDK 连接管理器将:

*   自动监视文件上传
*   观察互联网连接短时间中断的情况
*   当互联网恢复时自动继续发送文件
*   每次互联网恢复时重置连接管理器

只要总断开时间不超过 45 秒，Sendbird SDK 就会继续尝试发送视频文件数据。除了 iOS SDK 之外，此循环将无限期继续，iOS SDK 将在文件第一部分成功发送五分钟后超时并停止上传。

如果连接管理器的事件处理程序激发 onReconnectFailed()，则所有上载文件的尝试都将停止，sendFileMessage 回调将返回以下错误消息:

文件上传超时错误

在 onReconnectFailed()处理程序中，考虑向用户指示连接已丢失，并自动尝试调用 sendbird.reconnect()或为用户提供手动调用 sendbird.reconnect()的选项。重新连接后，考虑向用户提供重试发送文件消息的选项。在大多数情况下，Sendbird 的连接管理器会无限尝试重新连接。因此，在 sendFileMessage()回调中侦听文件消息发送失败。

一旦文件上传开始，如果用户锁定屏幕或将应用程序移到后台，只要有互联网连接，文件上传过程就会继续。

## 超大视频文件

想象一下，如果用户试图发送一个大于 Sendbird 计划中设置的文件大小限制的文件消息，用户会有怎样的体验。默认情况下，最大文件大小为 5MB。如果您的最大文件大小是预先确定的，请考虑在用户尝试发送文件之前计算文件大小。如果视频文件过大，警告用户。

如果尝试的文件上传超过最大允许限制，Sendbird 的服务器会发送一个错误。例如:

**上传的最大文件大小为 25MB。代码= 400111。**

仅在上传完成后才会触发警告消息。对于 iOS，考虑设置 sbd options . setfiletransfertime(300)。这样做可以防止设备在大量上传时超时。

如果用户希望在任何时候取消上传，他们可以使用 cancelUploadingFileMessage()来完成。(这是针对 iOS、Android 和 JavaScript 的)

## 使用 Sendbird 文件消息上传视频文件

请参见每个操作系统的以下说明:

*   [iOS](https://sendbird.com/docs/chat/v4/ios/guides/group-channel#2-send-a-message)
*   [**安卓**](https://sendbird.com/docs/chat/v4/android/guides/group-channel#2-send-a-message)
*   [**JavaScript**](https://sendbird.com/docs/chat/v4/javascript/guides/group-channel#2-send-a-message)

## 视频文件消息对象

GIF 文件消息示例对象(包括可选的静态缩略图):

# 处理邮件中的视频文件

考虑如何通知用户他们有一条包含视频文件的消息。在上面的示例文件消息对象中有一个名为 file.type 的字段。向用户传送视频时，下载和播放可以同时进行，或者文件可以在下载完成后播放。使用以下 Sendbird 示例来了解如何实现视频中文件消息的处理:

*   [**iOS**](https://github.com/sendbird/sendbird-chat-sample-ios)
*   [安卓 ](https://github.com/sendbird/sendbird-chat-sample-android)
*   [**JavaScript**](https://github.com/sendbird/sendbird-chat-sample-react)

# 发送 gif

现在来说说发 gif。Sendbird 的文件上传服务允许任何数据文件上传并存储在 Sendbird 中。Sendbird 的文件消息存储内部和外部数据的 URL 链接。

## 文件上传大小限制

如果你直接上传一个文件到 Sendbird，每个文件有一个大小限制，这取决于你的 Sendbird 计划。如果您上传的文件超出了限制，您将会看到如下错误:

**上传的最大文件大小为 25 毫克。代码= 400111。**

请注意，如果您发送带有 URL 的文件消息，Sendbird 中没有大小限制。

要了解您的计划允许什么，请访问您的 [**Sendbird 仪表盘**](https://dashboard.sendbird.com/auth/signin) 。如果您需要更高的尺寸限制， [**联系仙鸟销售团队**](https://sendbird.com/contact-sales) 。

## 拇指甲

如果您的应用程序使用 Sendbird premium 缩略图生成工具，您可以选择为您的文件生成缩略图。自动生成的缩略图是 gif 的静态小图像。

## 适度

Sendbird 的图像审核功能适用于 GIF 文件。然而，可以看出，在使用谷歌的 Vision API try-it 工具时，处理 GIF 图像所需的时间明显长于常规图像。

## 使用 Sendbird 文件消息上传 GIF 文件

*   [**iOS**](https://sendbird.com/docs/chat/v4/ios/guides/group-channel#2-send-a-message)
*   [**安卓**](https://sendbird.com/docs/chat/v4/android/guides/group-channel#2-send-a-message)
*   [Javascript](https://sendbird.com/docs/chat/v4/javascript/guides/group-channel#2-send-a-message)

## GIF 文件消息示例对象，包括可选的静态缩略图:

以下是各种操作系统的一些资源:

**iOS**

*   [**取文件报文**](https://sendbird.com/docs/chat/v4/ios/guides/group-channel#2-load-previous-messages)
*   [**通过通道事件代理**](https://sendbird.com/docs/chat/v4/ios/guides/event-delegate#1-event-delegate) 监听消息
*   [**堆栈溢出:如何在 Swift 中加载 GIF 图片**](https://stackoverflow.com/questions/27919620/how-to-load-gif-image-in-swift/27922518#27922518)

**安卓**

*   [**获取文件信息**](https://sendbird.com/docs/chat/v4/android/guides/group-channel#2-load-previous-messages)
*   [**通过通道处理器**](https://sendbird.com/docs/chat/v4/android/guides/event-handler#1-event-handler) 监听消息
*   [**使用滑动或高效图像加载**](https://bumptech.github.io/glide)
*   [**Glide 和 gif 教程**](https://futurestud.io/tutorials/glide-displaying-gifs-and-videos)

**JavaScript**

*   [**获取文件消息**](https://sendbird.com/docs/chat/v4/javascript/guides/group-channel#2-load-previous-messages)
*   [**通过通道处理器**](https://sendbird.com/docs/chat/v4/javascript/guides/event-handler#1-event-handler) 监听消息

发送 GIF 文件消息的快捷示例:

一个 Android 示例，摘自 Sendbird 的 Android 基本示例:

# 创建缩略图

对于高级会员，Sendbird 支持为文件类型为 image/^或 video/*的视频和图片生成缩略图。发送图像文件时，您可以决定是否创建图像的缩略图以在您的 UI 中呈现。您最多可以指定三个维度来生成缩略图，这可以支持各种显示密度。

以下是为每个操作系统创建缩略图的一些细节。

## 机器人

sendFileMessage()方法要求您传递一个文件消息列表。缩略图大小的对象。每个对象都可以用 ThumbnailSize 构造函数创建，其中提供的值确定最大尺寸的像素。onSent()回调随后返回包含生成的缩略图图像文件 URL 的缩略图对象列表。

以下是 Java 概述:

缩略图图像被生成以均匀地适合(maxWidth，maxHeight)的边界，这是通过 ThumbnailSize 构造函数提供的。请注意，如果原始图像小于指定的尺寸，缩略图将不会调整大小。getUrl()返回生成的缩略图文件在 Sendbird 服务器中的位置。

## ios

sendFileMessage()方法需要将 SBDThumbnailSize 对象的 NSArray 作为参数传递。可以使用 SBDThumbnailSize makeWithMaxCGSize 或 SBDThumbnailSize makeWithMaxWidth:max height:constructors 创建每个对象，其中提供的值确定缩略图最大尺寸的像素。completionHandler: … : callback 随后返回包含生成的缩略图图像文件的 URL 的 SBDThumbnail 对象的 NSArray。

**Swift 概述**:

**目标-C:**

生成一个缩略图以均匀地适合(maxWidth，maxHeight)的界限，这些界限是通过构造函数提供的。请注意，如果原始图像小于指定的尺寸，则不会调整缩略图的大小。URL 返回 Sendbird 服务器中生成的缩略图文件的位置。

## Java Script 语言

sendFileMessage()方法需要传递一个包含缩略图的最大宽度和高度值的项目数组。回调函数随后返回包含所生成的缩略图图像文件的 URL 的缩略图项目列表。这不包括确保支持更多浏览器的 ES6 语法。

生成一个缩略图，以均匀地适合所提供的(maxWidth，maxHeight)的边界。请注意，如果原始图像小于指定的尺寸，则不会调整缩略图的大小。URL 返回 Sendbird 服务器中生成的缩略图文件的位置。

## 图像上传超时注意事项

通过稳定的网络连接，上传图像和视频文件不会有任何问题。但值得考虑以下几点:

**图片上传和 Sendbird 消息速率限制**

考虑以相册的形式添加多个图像的用例。用 Sendbird 实现这种特性的一个基本限制是，发送任何消息都被限制为每秒五条消息，每条消息一个文件。如果你上传多张图片，注意不要超过这个限制。

## 进一步的实施考虑

考虑将 Sendbird 的文件上传与进度处理程序一起使用:

SendFileMessagesWithProgressHandler—JavaScript *， [**iOS**](https://sendbird.com/docs/chat/v4/ios/guides/group-channel-advanced#2-track-file-upload-progress-using-a-handler) ， [**Android**](https://sendbird.com/docs/chat/v4/android/guides/group-channel-advanced#2-track-file-upload-progress-using-a-handler)

***注意*** *: JavaScript 函数在 v4 文档中已弃用。*

此外，考虑为失败的上传实现重试功能。失败的上传将在文件消息完成/回调处理程序中提供错误。

**获取并显示缩略图**

Sendbird 文件消息不包含任何图像数据。相反，它包含引用存储在 Sendbird 或您的图像存储设备中的图像的 URL。如果部署了缩略图，每个缩略图版本也将有自己的 URL。考虑一些优化，比如获取最小的缩略图，然后用 Glide(在 Android 中)、NSCache(在 iOS 中)和 Cache API 服务 Worker(在 JavaScript 中)等服务进行缓存。

# 结论

恭喜你！本指南为您提供了设计和使用消息对象的实践信息。您正在为您的用户创造一个迷人而有趣的聊天体验！

快乐的应用内聊天编码！✌️