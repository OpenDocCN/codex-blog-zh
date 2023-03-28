# 交流 API 资源创建

> 原文：<https://medium.com/codex/communicating-resource-creation-60893f108623?source=collection_archive---------24----------------------->

![](img/599d70c3302b8b37fc1ad72d29d3a251.png)

照片由 [Devi Puspita Amartha Yahya](https://unsplash.com/@deviyahya?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在[我的上一篇文章](https://dillonredding.medium.com/the-empty-search-result-anti-pattern-f23c140ab871)之后，我想我应该再写一篇关于 HTTP 礼仪的文章，这一次是关于 API 中常见的误用`[201 (Created)](https://datatracker.ietf.org/doc/html/rfc7231#section-6.3.2)`状态代码。这可能有点棘手，因为虽然您可能正确地使用了状态代码本身，但是消息的另一部分可能有您可能没有意识到的含义。

我看到的最常用的方式是响应向集合追加项目的`POST`请求。大概是这样的:

我们在`/books`有一个收藏，我们想给它添加一本书。典型的反应通常很简单:

在响应中，我们通常在表示中获得某种服务器生成的标识符(在上面的例子中是`id`)。这与 HTTP 无关，但是我们的 API 通常试图传达的是在`/books/1234`有一个新的资源，然而这种方法产生了一些问题。

首先，客户需要理解服务器的 URL 设计策略。为了以后访问新资源，他们必须知道获取标识符的值，并将其作为路径段附加到集合的 URL。(如果您的媒体类型定义了这种行为，那没问题，但是我主要关注使用普通 JSON 的传统“REST”API。在以后的文章中会有更多的介绍。)这一要求将客户端耦合到服务器，因为[URL 是不透明的](https://www.w3.org/DesignIssues/Axioms.html#opaque)，因此是服务器的实现细节。

其次，也是这篇文章的重点，是协议层面的问题。为了更好地理解这一点，让我们看看 HTTP 标准[中`201`状态代码的定义:](https://datatracker.ietf.org/doc/html/rfc7231)

> *`*201 (Created)*`*状态代码表示请求已经完成，并导致一个或多个新资源被创建。由请求创建的主要资源由响应中的* `*Location*` *报头字段标识，或者如果没有接收到* `*Location*` *字段，则由有效请求 URI 标识。**

*由于我们上面的响应缺少了一个`[Location](https://datatracker.ietf.org/doc/html/rfc7231#section-7.1.2)`头，我们的服务器*实际上*说的是在`/books`创建了一个新资源。显然，事实并非如此。那是我们的收藏，早就有了。因此，更合适的反应如下:*

*这表示为了响应我们的请求，在`http://example.com/books/9781449358068`创建了一个新资源。我们本来可以发送回绝对 URL，但是 HTTP 标准将`Location`头定义为一个 [URI 引用](https://datatracker.ietf.org/doc/html/rfc3986#section-4.1)，当它是一个[相对引用](https://datatracker.ietf.org/doc/html/rfc3986#section-4.2)时，它是针对[有效请求 URI](https://datatracker.ietf.org/doc/html/rfc7230#section-5.5) : `http://example.com/books`的[解析](https://datatracker.ietf.org/doc/html/rfc3986#section-5)。*

*我们还可以在响应中包含表示，但是现在客户端已经有了新资源的 URL，他们不需要标识符，因为这是我们在响应中添加到表示中的所有内容，所以客户端不需要它，我们在这个过程中节省了一些网络带宽。此外，我们通过减少客户端理解服务器 URL 的需求来放松耦合。*

# *`201`不带`Location`*

*我们看到不包括一个`Location` *在技术上是有效的，但是我们什么时候想要这样做呢？一个原因是让客户控制 URI，但是*为什么*我们要这么做？也许我们不想为每个资源生成一个 URL。**

*回到我们的图书示例，假设我们希望允许客户端创建表示在`/wish-lists/{id}`找到的图书的期望列表的资源，但是不是服务器为`id`生成值，而是让客户端选择它。*

*您可能熟悉使用`PUT`来更新资源，但是根据 HTTP 标准( [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4.3.4) )，一个`PUT`请求要求“目标资源是*创建的*或替换的【强调矿】”。它接着说“如果目标资源*没有*的当前表示，并且`PUT` *成功地创建了一个*，那么源服务器必须通过发送`201 (Created)`响应来通知用户代理。”因此，如果一个资源在特定的 URL 上不存在，可以使用一个`PUT`来创建它(假设服务器允许)。*

*因此，创建愿望列表的请求可能如下所示:*

*假设`/wish-lists/classics`处的资源没有表示(即它不存在)，服务器创建它并通知客户机:*

*没有`Location`头，这个响应表示在[有效请求 URI](https://datatracker.ietf.org/doc/html/rfc7230#section-5.5) : `http://example.com/wish-lists/classics`处成功创建了资源。*

*此外，我们的服务器可以允许类似的请求在资源被创建后*更新*资源。对于`PUT`请求，“如果目标资源*具有当前表示，并且该表示被成功修改[…]，那么源服务器必须发送`200 (OK)`或`204 (No Content)`响应”(RFC 7231)。**

# *摘要*

*当接受`POST`来创建一个条目并将其追加到集合中时，用一个`201`来响应并包含一个`Location`头。如果你不能包含一个`Location`头(或者只是不想)，就使用一个`200 (OK)`。谎报已经存在的资源的创建是没有价值的。*

*或者，使用`PUT`创建您的资源，在这种情况下，根本不用担心发送`Location`头。*

*我很乐意听到你对这篇文章的想法、评论和反馈，尤其是如果你已经看到了类似的东西。你可以通过 Twitter [@dillon_redding](https://twitter.com/dillon_redding) 联系我。*