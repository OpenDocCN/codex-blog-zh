# 用 Hilt 创建网络模块

> 原文：<https://medium.com/codex/creating-the-network-module-with-hilt-3eefc54b72?source=collection_archive---------3----------------------->

在前一篇博客中，我们了解了应用程序模块，以及如何使用 Hilt 注入应用程序实例。在这篇博文中，我们将了解如何使用 Hilt 设置网络模块，然后使用 MVVM 架构通过网络模块进行 API 调用。

![](img/c26f41d9c78dd120ecf5523acb33b064.png)

蒙罗工作室在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

你可以从这里跳到这个系列的任何部分

 [## 中等

### 网络模块中的句柄](/@priyansh-kedia/using-hilt-in-the-network-module-6ff8c50027d6)  [## 中等

### 创建视图模型依赖项](/@priyansh-kedia/creating-viewmodel-dependencies-using-hilt-ff8005488910) 

**网络模块👑**

我们首先创建一个简单的 Kotlin 类，并将其命名为 **NetworkModule** 。

我们使用 **@Module 和@InstallIn 注释这个类。你可以在这里阅读这些注解。我们首先创建一个帮助注入 Gson 的方法。我们将使用 Gson 对 JSON 对象进行序列化和反序列化。**

```
@Provides
@Singleton
fun getGson(): Gson {
    return GsonBuilder().serializeNulls().setLenient().create()
}
```

我们使用@Provides 和@Singleton 来注释上面的方法。这些注释在之前的博客中有简要的解释。

**serializeNulls()** 用于指定即使是空值也将被序列化。*默认情况下，Gson 省略空字段。*

**set letterance()**使 Gson 解析器在接受什么方面更加自由*。默认情况下，它是严格的，只接受 JSON。*

## 但是为什么我们首先创造了这种方法呢？

这是因为 Gson 依赖于我们将在 NetworkModule 中创建的其他方法之一。

接下来，我们将为网络拦截器创建方法。

## 拦截器方法

```
@Provides
@Singleton
fun getInterceptor(): Interceptor {
    return Interceptor **{** val request = **it**.request().newBuilder()
        val actualRequest = request.build()
        **it**.proceed(actualRequest)
    **}** }
```

上面的方法用于在我们即将创建的改进实例中注入拦截器。您还可以为所有请求添加标题。你可以这样做，

```
@Provides
@Singleton
fun getInterceptor(): Interceptor {
    return Interceptor **{** val request = **it**.request().newBuilder()
        request.addHeader("Authorization", "<Your token here>")
        val actualRequest = request.build()
        **it**.proceed(actualRequest)
    **}** }
```

接下来，我们将为 OkHttpClient 创建方法。

## OkHttpClient 方法

```
@Provides
    @Singleton
    fun getOkHttpClient(
        interceptor: Interceptor
    ): OkHttpClient {
        val httpBuilder = OkHttpClient.Builder()
            .addInterceptor(interceptor)
            .connectTimeout(30, TimeUnit.*SECONDS*)
            .readTimeout(30, TimeUnit.*SECONDS*)
            .writeTimeout(50, TimeUnit.*SECONDS*)

        return httpBuilder
            .protocols(*mutableListOf*(Protocol.*HTTP_1_1*))
            .build()
    }
```

OkHttpClient 的所有这些设置都指定了读取、写入和连接超时。你也可以添加一个类似 [Stetho](http://facebook.github.io/stetho/) 的网络拦截器。这可以这样做，

```
@Provides
    @Singleton
    fun getOkHttpClient(
        interceptor: Interceptor
    ): OkHttpClient {
        val httpBuilder = OkHttpClient.Builder()
            .addInterceptor(interceptor)
 **.addNetworkInterceptor(StethoInterceptor())**            .connectTimeout(30, TimeUnit.*SECONDS*)
            .readTimeout(30, TimeUnit.*SECONDS*)
            .writeTimeout(50, TimeUnit.*SECONDS*)

        return httpBuilder
            .protocols(*mutableListOf*(Protocol.*HTTP_1_1*))
            .build()
    }
```

您可以看到拦截器是对 **getOkHttpClient** 方法的依赖。使用 Dagger 注入，我们不需要单独创建这些依赖项。

我们现在创建用于翻新的方法，该方法用于建立与服务器的网络连接。

## 改造方法

```
@Provides
@Singleton
fun getRetrofit(
    client: OkHttpClient,
    gson: Gson
): Retrofit {
    return Retrofit.Builder()
        .baseUrl(BuildConfig.*BASE_URL*)
        .addConverterFactory(GsonConverterFactory.create(gson))
        .client(client)
        .build()
}
```

我们将转换器工厂添加到改造中，添加基本 URL 和 OkHttpClient。可以看到 Gson 和 OkHttpClient 是这个函数的依赖项。

最后，我们将创建一个方法，帮助我们连接 API 端点的接口方法。

**API client 方法**

```
@Provides
@Singleton
fun getApiClient(retrofit: Retrofit): ApiClient {
    return retrofit.create(ApiClient::class.*java*)
}
```

这个方法简单地创建了一个 ApiClient 实例，方法是将 retrofit 连接到它。

我们将看到如何创建 ApiClient 接口，以及如何创建视图模型的依赖关系。

从[这里](/@priyansh-kedia/creating-viewmodel-dependencies-using-hilt-ff8005488910)跳到下一篇博客。

**参考文献**

 [## 加装

### 接口方法及其参数上的注释表明了如何处理请求。请求方法每隔…

square.github.io](https://square.github.io/retrofit/)