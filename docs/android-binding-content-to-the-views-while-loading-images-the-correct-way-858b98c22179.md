# Android:在正确加载图像的同时将内容绑定到视图

> 原文：<https://medium.com/codex/android-binding-content-to-the-views-while-loading-images-the-correct-way-858b98c22179?source=collection_archive---------11----------------------->

![](img/3e98c8af4a1537882ab5bece2d1f2e86.png)

苏珊·Q·尹在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在 Android 中处理图像是 Android 应用程序开发中最常见的部分之一。图像可能来自网络或设备的本地存储。将图像加载到视图中需要有效，以便使用更少的资源，并优雅地呈现视图。有些情况下，我们希望从 web 或数据库中加载带有图像的其他内容，或者希望在图像下载后执行某些操作。

有许多图书馆通过提供高效的图像下载和缓存功能让我们的生活变得轻松， [Glide](https://bumptech.github.io/glide/) 就是其中之一。Glide 可以加载和显示来自许多来源的图像，同时还负责缓存，并在处理图像时保持较低的内存影响。谷歌官方应用程序也在使用 Glide。

Glide 支持获取、解码和显示视频静止图像、图像和动画 gif。Glide 包括一个灵活的 api，允许开发人员插入几乎任何网络堆栈。默认情况下，Glide 使用基于自定义 HttpUrlConnection 的堆栈，但也包括插入到 Google 的凌空项目或 Square 的 OkHttp 库中的实用程序库。

Glide 的主要目的是尽可能平滑和快速地滚动任何类型的图像列表，但 Glide 对于我们需要获取、调整和显示远程图像的几乎任何情况都是有效的。

对于我们的例子，我们将处理从网上下载的图像以及相应的文本内容。在大多数情况下，文本内容会在图像下载之前下载。为了给用户更好的体验，我们将同时展示它们。

# **滑行依赖**

```
dependencies **{** def glideVersion = '4.11.0'
    implementation "com.github.bumptech.glide:glide:$glideVersion"**}**
```

# Android 清单权限

```
<uses-permission android:name="android.permission.**INTERNET**" />
<uses-permission android:name="android.permission.**ACCESS_NETWORK_STATE**" />
```

我们需要添加**互联网**权限，让我们的应用程序访问网络。当我们继续这篇文章时，我将解释对 **ACCESS_NETWORK_STATE** 权限的需求。

# **Glide 的基本实现**

```
Glide.with(fragment)
    .load(imageUrl)
    .error(R.drawable.*ic_error*)
    .into(imageView)
```

在上面的代码片段中，我们只是给了它一个**片段**的引用，其中有 **imageView** 。我们将图像 url 传递给 **load(..)**功能，为错误情况设置一个 drawable。您可以使用 Glide 访问多种功能，但这超出了本文的范围。

# **添加下载监听器**

```
Glide.with(fragment)
    .load(imageUrl)
    .error(R.drawable.*ic_error*)
    **.listener(object : RequestListener<Drawable> {
        override fun onLoadFailed(
            e: GlideException?,
            model: Any?,
            target: Target<Drawable>?,
            isFirstResource: Boolean
        ): Boolean {
            *TODO*("Not yet implemented")
        }

        override fun onResourceReady(
            resource: Drawable?,
            model: Any?,
            target: Target<Drawable>?,
            dataSource: DataSource?,
            isFirstResource: Boolean
        ): Boolean {
            *TODO*("Not yet implemented")
        }

    })**
    .into(imageView)
```

这里我们添加了一个侦听器，它为我们提供了两个回调，一个用于失败案例 **onLoadFailed(..)、**等为成功案例 **onResourceReady(..).**我们需要做 onResourceReady 中视图的渲染(..)方法。

```
Glide.with(fragment)
    .load(imageUrl)
    .error(R.drawable.*ic_error*)
    .listener(object : RequestListener<Drawable> {
        override fun onLoadFailed(
            e: GlideException?,
            model: Any?,
            target: Target<Drawable>?,
            isFirstResource: Boolean
        ): Boolean {
            **pbImage.*isVisible* = false
            return false**
        }

        override fun onResourceReady(
            resource: Drawable?,
            model: Any?,
            target: Target<Drawable>?,
            dataSource: DataSource?,
            isFirstResource: Boolean
        ): Boolean {
            **pbImage.*isVisible* = false
            txtAuthor.*isVisible* = true
            txtDescription.*isVisible* = image.description != null
            return false**
        }

    })
    .into(imageView)
```

这里 **pbImage** 是我们 ProgressBar 的 id， **txtAuthor** 和 **txtDescription** 是我们 TextViews 的 id， **imageView** 是我们 imageView 的 id。如果响应失败，我们将进度条的可见性设置为不可见，并返回 false。在成功响应的情况下，我们同时将数据绑定到所有视图，为用户提供更好的体验。我们需要从两个回调方法返回 false，以便将图像加载到 **imageView** 视图中。

这就是我们如何更优雅地加载图像数据。

# 处理网络错误

考虑这样一种情况，由于网络错误，图像无法加载到视图中。为了确保在我们的设备可以访问网络时加载图像，我们需要 **ACCESS_NETWORK_STATE** 权限，Glide 会处理剩下的事情。是的，就是这么简单。

我们的文章写完了。

希望你学到了一些东西。干杯！