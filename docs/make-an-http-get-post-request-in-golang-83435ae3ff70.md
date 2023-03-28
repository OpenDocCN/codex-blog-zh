# 用 Golang 发出 HTTP GET/POST 请求

> 原文：<https://medium.com/codex/make-an-http-get-post-request-in-golang-83435ae3ff70?source=collection_archive---------2----------------------->

## 戈朗发展，[抄本](http://medium.com/codex)

## 发出 REST API 请求并处理响应

![](img/e8bb1cf16cb4a572ca22e2c6ef67a5a4.png)

照片由[詹姆斯·哈里森](https://unsplash.com/@jstrippa?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

HTTP 请求是任何应用程序的基础。在这个例子中，我将向您展示如何使用 Golang 进行 GET/POST 请求。

我们将使用 jsonplaceholder.typicode.com API 向。

## 发出 GET 请求

```
package mainimport (
 "io/ioutil"
 "log"
 "net/http"
)func main() {
  resp, err := http.Get("[https://jsonplaceholder.typicode.com/todos/1](https://jsonplaceholder.typicode.com/todos/1)")

  if err != nil {
     log.Printf("Request Failed: %s", err)
     return
  } defer resp.Body.Close()
  body, err := ioutil.ReadAll(resp.Body) if err != nil {
     log.Printf("Reading body failed: %s", err)
     return
  } // Log the request body 
  bodyString := string(body)
  log.Print(bodyString)
}
```

## 使用参数发出 GET 请求

让我们看看如何将 URL 参数添加到之前的 GET 请求中。我们可以传递一个`userId`来获取该用户的帖子。

```
package mainimport (
 "encoding/json"
 "fmt"
 "io/ioutil"
 "log"
 "net/http"
 "net/url"
)type Posts []struct {
 Userid int    `json:"userId"`
 ID     int    `json:"id"`
 Title  string `json:"title"`
 Body   string `json:"body"`
}func main() {
  params := url.Values{}
  params.Add("userId", "1") resp, err := http.Get("[https://jsonplaceholder.typicode.com/posts](https://jsonplaceholder.typicode.com/posts)?" +params.Encode()) if err != nil {
     log.Printf("Request Failed: %s", err)
     return
  }
  defer resp.Body.Close()
  body, err := ioutil.ReadAll(resp.Body) // Log the request body  
  bodyString := string(body)
  log.Print(bodyString) // Unmarshal result
  posts := Posts{}
  err = json.Unmarshal(body, &posts)
  if err != nil {
     log.Printf("Reading body failed: %s", err)
     return
  } log.Printf("The title of the first post is %s", posts[0].Title)
}
```

我们还可以将 JSON 响应封送到 post 结构的集合中。作为参考，来自 API 的 JSON 响应如下所示:

```
[
  {
    "userId": 1,
    "id": 1,
    "title": "sunt aut facere repellat provident occaecati excepturi optio reprehenderit",
    "body": "quia et suscipit suscipit recusandae consequuntur expedita et cum reprehenderit molestiae ut ut quas totam nostrum rerum est autem sunt rem eveniet architecto"
  }
]
```

## 提出发布请求

类似地，我们也可以使用主体参数发出 post 请求，以使用 API 创建 POST。

```
package mainimport (
 "encoding/json"
 "io/ioutil"
 "log"
 "net/http"
 "net/url"
)type Post struct {
 Userid string `json:"userId"`
 ID     int    `json:"id"`
 Title  string `json:"title"`
 Body   string `json:"body"`
}func main() {
  params := url.Values{}
  params.Add("title", "foo")
  params.Add("body", "bar")
  params.Add("userId", "1")resp, err := http.PostForm("[https://jsonplaceholder.typicode.com/posts](https://jsonplaceholder.typicode.com/posts)",
  params) if err != nil {   
   log.Printf("Request Failed: %s", err)
   return
 } defer resp.Body.Close()
  body, err := ioutil.ReadAll(resp.Body) // Log the request body 
  bodyString := string(body)
  log.Print(bodyString) // Unmarshal result
  post := Post{}
  err = json.Unmarshal(body, &post)
  if err != nil {
     log.Printf("Reading body failed: %s", err)
     return
  }

  log.Printf("Post added with ID %d", post.ID)
}
```

API POST 请求的响应如下所示:

```
{
  id: 101,
  title: 'foo',
  body: 'bar',
  userId: 1
}
```

这就是了。现在，您可以使用 Golang 的 GET/POST 请求访问整个 web 上的数据。

在 [Twitter](https://twitter.com/sssaini_) 上找到我，我们聊聊:)