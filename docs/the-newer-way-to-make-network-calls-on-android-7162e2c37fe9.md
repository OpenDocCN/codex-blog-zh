# 在 Android 上打网络电话的新方式

> 原文：<https://medium.com/codex/the-newer-way-to-make-network-calls-on-android-7162e2c37fe9?source=collection_archive---------1----------------------->

![](img/3f40cc765060038d9be015bd3b5c829c.png)

ktor-Android 客户端

我们使用诸如翻新和凌空技术的时间太长了，行业也非常有效地适应了这些技术。尽管如此，随着 Kotlin 在 Android 开发人员中的受欢迎程度的急剧增长，Kotlin 中出现了一个新的网络调用 API:**Ktor-client**。

JetBrains 每天都在快速开发 Kotlin。它是世界上最受欢迎的语言之一，占据了 Android 开发者的心。然而，他们并没有把它限制在那里。我们也可以用 Kotlin 创建一个后端服务器，它的 API 叫做 **Ktor** ，而 **Ktor-client** 是它的客户端版本。

# 设置 Ktor 客户端

像往常一样，我们需要安装 Ktor-client 的依赖项。对于本教程，我们将使用以下依赖关系:

```
def ktor_version = "1.6.8"
implementation "io.ktor:ktor-client-core:$ktor_version"
implementation "io.ktor:ktor-client-android:$ktor_version"
implementation "io.ktor:ktor-client-serialization:$ktor_version"
implementation "io.ktor:ktor-client-logging:$ktor_version"
implementation "ch.qos.logback:logback-classic:1.2.9"
```

Ktor-client 提倡使用实现模块，因此我们只使用需要的模块，而不是 Ktor 中的所有模块。

# 创建界面

虽然使用 **Ktor-client** 并不需要创建接口，但是使用这种设计模式是一个很好的实践。

```
interface ApiService {
  suspend fun getPost(id: String): Response<PostDto> suspend fun createPost(post: Post): Response<PostDto>
}
```

我们现在可以在一个具体的类中实现这个接口。这是我们将进行实际网络调用的地方。

```
class ApiServiceImpl(
  private val client: HttpClient
) : ApiService {
  override suspend fun getPost(id: String): Response<PostDto> {
    return try {
      val response = client.get<PostDto> {
        url("$BASE_URL/posts/$id/?key=value")
      }
      return Response.Success(response)
    } catch (e: RedirectResponseException) { // 3xx 
      Response.Error(e.response.status.description)
    } catch (e: ClientRequestException) { // 4xx
      Response.Error(e.response.status.description)
    } catch (e: ServerResponseException) { // 5xx
      Response.Error(e.response.status.description)
    }
  } [...]
}
```

Ktor-client 是一个基于 Kotlin 的库，使用了它提供的所有特性。为了进行一个`GET` HTTP 调用，我们在`client`对象上使用了`suspend fun get()`函数。该函数会将传入的数据解析为泛型参数。构建器 lambda 函数有一个`url()`方法，该方法接受带有路径参数和查询参数的`String`URL——原始 URL。

# 创建客户端对象

通常，我会鼓励你用依赖注入来做这件事，但是现在，我将在`ApiService`接口中做一个`companion object`。

```
interface ApiService {
  [...] companion object {
    val client = HttpClient(Android) {
      install(Logging) {
        level = LogLevel.ALL
      }      install(JsonFeature) {                
        serializer = KotlinxSerializer()
      }
    }
  }
}
```

# 包装它

现在，您可以结束所有工作，开始网络通话。假设您希望在用户点击按钮时获得帖子:

```
btnGetPost.setOnClickListener { _ ->
  lifecycleScope.launch {
    ApiService.client.use { 
      val service = ApiServiceImpl(it)

      when (val response = service.getPost("2")) {
        is Response.Success -> { /* do something */ }
        is Response.Error -> toast(response.message)
      }
    }
  }
}
```

# 提出发布请求

这就像发出一个`get`请求一样简单。

```
override suspend fun createPost(post: Post): Response<PostDto> {
  try {
    val response = client.post<PostDto> {
      url("$BASE_URL/posts")
      contentType(ContentType.Application.Json)
      setBody(post)
    }
   } catch ([...]) {
     [ same as in the get function ]
   }
}
```

我们使用`post`方法代替`get`。我们指定内容类型并传递请求体。

# 杂项笔记

1.  确保所有使用的`data class`都标有注释`@kotlinx.serialization.Serializable`
2.  上面代码中使用的`Response`类如下:

```
sealed class Response<T> {    
  data class Success<T>(val data: T) : Response<T>()    
  data class Error<T>(val message: String) : Response<T>()
}
```

3.不要忘记在 AndroidManifest 文件中添加`INTERNET`权限。

# 结论

**Ktor-client** 超级容易配置，超级容易使用。HTTP 方法或部分 URL 没有注释。这是一个简单的网络请求调用，它返回我们需要处理的数据类对象。