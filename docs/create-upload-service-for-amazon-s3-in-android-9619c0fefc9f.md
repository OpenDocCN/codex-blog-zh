# 在安卓系统中为亚马逊 S3 创建上传服务

> 原文：<https://medium.com/codex/create-upload-service-for-amazon-s3-in-android-9619c0fefc9f?source=collection_archive---------1----------------------->

图像是任何应用程序的重要组成部分。将图像上传到服务器，然后上传到像亚马逊 S3 这样的存储桶，可能会非常耗时。为此，亚马逊提供了自己的 SDK，将图片直接上传到 S3 桶。

在这篇博客中，我们将看看如何创建一个后台服务来上传图片到 S3，并使用 RxJava 显示一个进度条。

![](img/9af4ad9e633348ef65027ea890ea96ab.png)

# 设置依赖关系

RxJava 和 Amazon SDK 的依赖关系

将上述依赖项添加到您的应用程序级 build.gradle 文件中。

# 创建文件状态类

现在，我们将继续为文件上传的状态创建一个类。

UploadFileStatus.kt

这是一个简单的类，我们将使用来自 S3 SDK 的回调来检查上传状态。

# 创建 PublishSubject 实例

```
object LiveSubject {
    val FILE_UPLOAD_FILE: PublishSubject<UploadFileStatus> = PublishSubject.create()
}
```

创建一个新文件，将其命名为 LiveSubject，并添加给定的代码。

在上面的代码中，您可以看到我们创建了 PublishSubject 的一个实例，并指定了我们要传递的数据类型。

根据定义，发布主题是

```
*A Subject that emits items to currently subscribed Observers and terminal events to current.*
```

RxJava 帮助您简化复杂并发行为的实现。

# 创建上传服务类

我们首先创建一个名为 UploadService.kt 的新文件，它包含一个扩展了`**JobIntentService**` **的类`**UploadService**`。**

在这个类中，我们将创建一个伴随对象(用于静态使用)，并在其中添加一个简单的函数。

将从我们的活动/片段中调用`enqueueWork`,将图像上传到亚马逊 S3 桶。

现在我们将在`UploadService`中看到代码

`onHandleWork`是一个覆盖方法，默认情况下在后台处理任务。

在`onHandleWork`里面，我们只是，

```
1\. Get data from the intent
2\. Get the image from the device
3\. Create a file using the image
4\. Extract the extension from the file
```

并将这些参数发送给`s3Upload`函数。

在`s3Upload`函数内部，我们编写一些直接取自 SDK 文档的代码，这需要我们提供我们的`AWS_SECRET_KEY`和`AWS_ACCESS_KEY`以及我们的`AWS_ENDPOINT`。

重要的是要看`transferListener`。这有三个回调，`onStateChanged`、`onProgressChanged`和`onError`。

```
onStateChanged :- When the upload state changes from not started, to started and so on.
onProgressChanged :- Regularly invoked with the changing progress of the file upload
onError :- Invoked in case of error
```

在`onProgressChanged`中，我们得到了上传的当前进度，如下所示

```
val status = (((current.toDouble() / total) * 100.0).toInt())                LiveSubject.FILE_UPLOAD_FILE.onNext(UploadFileStatus.FileStatus(status))
```

我们通过将当前上传的字节数除以总字节数来获得状态。

> 假设我们的文件是 1000 字节，目前已经上传了 500 字节，那么我们的进度是 50%。

然后我们在`PublishSubject`中发出当前的进度。

上传完成后，调用`onStateChanged`,我们创建最终的文件 URL，如下所示

```
val url = "${secrets.s3BaseUrl}/$fileName.$extension"
```

其中 s3BaseUrl 是 S3 存储桶的基本 Url。

# 订阅 RxJava 主题

上面的代码展示了如何观察 RxJava 主题，在进度条中显示进度，并在上传完成时获得最终的 URL。

# 调用上传服务

现在我们已经创建了上传服务，我们如何调用它呢？

```
val intent = Intent(this, UploadService::class.*java*)
intent.putExtra(UploadService.IMAGE_URI, **uri**)
UploadService.enqueueWork(this, intent)
```

使用上面的三行代码，你可以很容易地上传你的图片。`uri`是您从设备中选取的图像的 URI。

这就是我们所需要做的，来编写一个干净高效的代码，将文件上传到 S3 桶。

![](img/24c3bade604a8cff8147806ff08735c2.png)

S3 上传的演示应用程序

# 📚参考

 [## 亚马逊 S3 REST API 简介

### 解释亚马逊 S3 API 操作，相关的请求和响应结构，以及错误代码，使您能够存储…

docs.aws.amazon.com](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html) [](http://reactivex.io/RxJava/3.x/javadoc/io/reactivex/rxjava3/subjects/PublishSubject.html) [## 发布主题(RxJava Javadoc 3.1.1)

### 当 onError(Throwable)被调用时，PublishSubject 进入一个终止状态并发出同样的 Throwable…

reactivex.io](http://reactivex.io/RxJava/3.x/javadoc/io/reactivex/rxjava3/subjects/PublishSubject.html)