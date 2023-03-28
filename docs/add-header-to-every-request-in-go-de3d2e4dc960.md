# 向 Go 中的每个请求添加标题

> 原文：<https://medium.com/codex/add-header-to-every-request-in-go-de3d2e4dc960?source=collection_archive---------7----------------------->

![](img/d01575e7cd90e61ecb20e43f1e53e25e.png)

对所有 HTTP 请求进行更改会很方便。你可能想添加一个 API 密钥或一些关于发送者的信息，如应用程序版本等。不管你为什么想这么做，你都有几个选择来实现目标。

第一种方法是构建一个工厂方法来添加所需的头。

```
func newRequest(endpoint string) *http.Request {
    req, _ := http.NewRequest(http.MethodGet, fmt.Sprintf("https://%s", endpoint), nil)
    req.Header.Add("x-api-key", "my-secret-token")
    return req
}
```

这非常简单明了，但是它要求你为每个 HTTP 方法创建一个新方法，或者直接调用另一个函数。每次创建新请求时，您都必须这样做。

```
func addHeaders(r *http.Request) {
	req.Header.Add("x-api-key", "my-secret-token")
}

// somewhere in your code

req, err := http.NewRequest(/* ... */)
if err != nil {
	return nil
}

addHeaders(req)

// do your job here
```

这种方法很简单，但是需要在所有容易出错的模块中反复重复相同的代码。使用[往返器](https://godoc.org/net/http#RoundTripper)可以更容易地获得类似的结果。

```
type transport struct {
	underlyingTransport http.RoundTripper
}

func (t *transport) RoundTrip(req *http.Request) (*http.Response, error) {
	req.Header.Add("x-api-key", "my-secret-token")
	return t.underlyingTransport.RoundTrip(req)
}
```

在创建 HTTP 客户端时

```
c := http.Client{Transport: &transport{ underlyingTransport: http.DefaultTransport } }
```

这看起来优雅，清晰，简单。然而，在文件中有一个注释

> 除了消费和关闭请求体之外，往返不应修改请求。

这意味着我们**不应该**以我们的方式改变请求。往返通常用于速率限制或缓存响应，这是更有效的用法。有没有其他方法可以做到这一点？当然——使用 Go 接口的强大功能。

你应该做的是用你使用的所有方法为`http.Client`写一个包装器/中间件，并在那里添加你的头文件。

```
type httpClient struct {
	c        http.Client
	apiToken string
}

func (c *httpClient) Get(url string) (resp *Response, err error) {
	req, err := http.NewRequest("GET", url, nil)
	if err != nil {
		return nil, err

	}

	return c.Do(req)

}

func (c *Client) Post(url, contentType string, body io.Reader) (resp *Response, err error) {
	req, err := http.NewRequest("POST", url, body)
	if err != nil {
		return nil, err

	}

	req.Header.Set("Content-Type", contentType)

	return c.Do(req)

}

func (c *httpClient) Do(req *Request) (*Response, error) {
	req.Header.Add("x-api-key", c.apiToken)
	return c.c.Do(req)

}
```

正如你所看到的，它需要更多的代码，但另一方面，它给了你更多的灵活性，让你知道我们在请求中改变了什么。

# 摘要

有几种方法可以达到相同或相似的效果。你应该选择哪一个？这取决于您的用例。总是试图找到最简单和可读性更强的方法。如果工厂方法对你来说很好，不要让你的代码过于复杂。当你的工厂方法变得越来越复杂时，考虑将它重构为更小的方法或者直接使用包装器。

[给我买杯咖啡](https://www.buymeacoffee.com/bklimczak)

*最初发表于*[*https://developer20.com*](https://developer20.com/add-header-to-every-request-in-go/)*。*