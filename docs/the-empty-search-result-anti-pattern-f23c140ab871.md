# 空搜索结果反模式

> 原文：<https://medium.com/codex/the-empty-search-result-anti-pattern-f23c140ab871?source=collection_archive---------15----------------------->

![](img/1e536d9bcb3ef8efb52c7d7597946f5b.png)

照片由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

前几天我在 Twitter 上看到了[这篇文章，我认为这将是我的第一篇博文，因为我曾经在我曾经工作的地方看到过完全相同的问题，对于什么是“正确”或“恰当”没有明确的共识。](https://twitter.com/apihandyman/status/1397638739128733696)

这个问题围绕着“空搜索结果”这个概念。也就是说，我们有一个代表其他资源(项目)集合的资源，我们希望找到符合一个或多个标准的项目。接下来的问题是:如果没有条目符合我们的标准，我们的响应的状态代码应该是什么？我们通常面临三种选择:`200 (OK)`、`204 (No Content)`和`404 (Not Found)`。

很容易从表面上理解 [HTTP 状态代码](https://www.iana.org/assignments/http-status-codes)并在不理解协议层语义的情况下使用它们。我明白了。我已经做到了。RFC 并不容易读懂。然而，忽略和重新定义消息组件会降低我们的服务器与网络中其他组件的互操作性。因此，我将依靠比我聪明得多的人编写的标准，主要是 RFC 7231。

# 放弃

在深入之前，我想做一个简短的声明:在帖子中并不清楚我们的[目标资源](https://datatracker.ietf.org/doc/html/rfc7231#section-2) `/users?name=spock`是过滤的集合还是单个项目。这对于确定正确的状态代码非常重要，所以我会考虑这两种情况。

您可能会看到`/users?name=spock`并按照下面的思路思考:“显然，这是一个经过过滤的集合。项目 URL 将是`/users/spock`。好吧，坚持住。无论是在 [RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986) 还是 [RFC 7230](https://datatracker.ietf.org/doc/html/rfc7230#section-2.7.1) 中，都没有要求我们*如何构造*我们的 URL。典型的`/collection`和`/collection/{id}` URL 模式只是被广泛采用的惯例。对于所有 HTTP 来说，条目 URL 可能是`/user/spock`、`/u/spock`，甚至是`/dXNlci9zcG9jaw==`。只要我们能把它映射到原始服务器上的一个独特的资源，URL 就无关紧要了。

我们分配给 URL 的*意味着*与除了原始服务器之外的任何人都无关。关键是 URL 仅仅是一个标识符。我们不应该试图从它的内容中寻找额外的信息。这就是所谓的[不透明公理](https://www.w3.org/DesignIssues/Axioms.html#opaque)。

# 选项

所以，回到我们的问题:`200`、`204`，还是`404`？

# 选项 1: `200`

让我们先来看看来自 [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-6.3.1) 的`200`的定义:

> *`*200 (OK)*`*状态码表示请求已成功。在* `*200*` *响应中发送的有效载荷取决于请求方法。**

*这并不奇怪，但我们看到，无论我们的有效载荷是什么，都取决于[请求方法](https://www.iana.org/assignments/http-methods)。我们使用的是`[GET](https://datatracker.ietf.org/doc/html/rfc7231#section-4.3.1)`，所以我们的响应负载将是“目标资源的表示[”。](https://datatracker.ietf.org/doc/html/rfc7231#section-3)*

*我们先假设`/users?name=spock`是我们过滤后的集合。因为使用该名称的用户不存在，所以我们的表示应该是一个空集合。我们可以用几种[媒体类型](https://www.iana.org/assignments/media-types)来表现这一点。在 [JSON](https://datatracker.ietf.org/doc/html/rfc8259) 中，我们可能会有这样的东西:*

*我们可以去掉外部对象，只使用空数组，但我认为这样可以更好地表达数组要保存的值。*

*由于我们似乎遵循集合模式——对一组相关资源的 CRUD 操作——我们可以使用[集合+JSON](http://amundsen.com/media-types/collection/) ，在这种情况下，我们的表示可能如下所示:*

*注意`/collection/items`处的空数组(见 [JSON 指针](https://datatracker.ietf.org/doc/html/rfc6901))。我们在这里得到了比普通 JSON 更多的信息，比如我们所代表的资源的 URL(`/collection/href`)和一个描述[如何搜索](http://amundsen.com/media-types/collection/format/#query-templates)的[查询对象](http://amundsen.com/media-types/collection/format/#arrays-queries) ( `/collection/queries/0`)。*

*或者我们可能想使用更简单的东西，比如 [CSV](https://en.wikipedia.org/wiki/Separation_of_concerns) :*

*甚至 [HTML](https://html.spec.whatwg.org/multipage) 也有办法用空的`ol`或`ul`元素来表示空的集合:*

*由于`ol`元素是空的，我添加了一个`p`元素来描述这种情况，以避免在浏览器中查看时页面是空白的。对于 API 来说，这很可能不会显示在浏览器中——我不建议任何人在他们的 API 中使用 HTML，至少作为主要的表示格式——我只是想确保表示有用。*

*也许你是 XML 的粉丝，我们可以简单地使用一个空元素:*

*尽管没有 XSD，XML 文档缺少类型信息。根据上下文的不同，*可能*不会马上明白`users`元素应该代表一个空集合，但是我不认为这对大多数人来说是一个障碍。*

*如果我们真的想偷懒，我们可以使用纯文本，借用 HTML 示例中的描述:*

*我们肯定还可以使用其他媒体类型，但我认为有足够多的例子表明我们可以如何展示我们的空集合。关键是我们有选择。因此，`200`似乎很有希望，但是为了便于讨论，让我们考虑另外两个状态代码。*

*然而，在继续之前，让我们快速考虑一下`/users?name=spock`是单一用户的情况。回想一下，用户不存在，因此资源不存在，我们无法表示它，因此`200`是不合适的。*

# *选项 2: `204`*

*现在为了`204`。在我们的“空搜索结果”的上下文中，我相信大多数人使用这个状态代码来表示搜索成功，但是没有符合给定标准的项目。好吧，让我们点击 [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-6.3.5) 获取定义:*

> **`*204 (No Content)*`*状态码表示服务器已成功完成请求，响应有效负载正文中没有要发送的附加内容。***

**在对一个`GET`请求的响应中，`204`似乎暗示我们不能表示目标资源。所以，不完全是一回事。**

**如果`/users?name=spock`是我们过滤后的集合，那么`204`将意味着我们没有表示，但是正如我们在[选项 1](https://dillonredding.github.io/2021/06/28/the-empty-search-result-anti-pattern.html#option-1-200) 中看到的，这不是真的。这也意味着我们甚至不能表示一个非空的集合，在这种情况下，这个资源不是很有用。所以，`204`在这里对我们不起作用。**

**另一方面，如果`/users?name=spock`是一个条目，那么`204`响应意味着我们甚至不能代表一个用户，使得这个资源完全无用。因此`204`在这两种情况下都不合适，因此在响应搜索集合时是一个反模式。**

# **选项 3: `404`**

**最后是`404`。同样，让我们从来自 [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-6.5.4) 的定义开始:**

> ***`*404 (Not Found)*`*状态代码表示源服务器没有找到目标资源的当前表示，或者不愿意公开其存在。****

**我们将忽略后半部分，因为这更多的是一个安全问题，但是前半部分意味着我们不能表示目标资源。听起来熟悉吗？这与`[204](https://dillonredding.github.io/2021/06/28/the-empty-search-result-anti-pattern.html#option-2-204)`[选项](https://dillonredding.github.io/2021/06/28/the-empty-search-result-anti-pattern.html#option-2-204)相同，只是增加了一个警告，这是*客户的问题*。**

**在`/users?name=spock`是被过滤的集合的情况下，它与`204`的参数相同。然而，在`404`的情况下，定义声明“原始服务器没有**找到**当前表示”。虽然我们确实有表示的选项，但这并不意味着服务器知道或理解这些选项。因此，从技术上讲，`404`*可能是有效的——这取决于我们对“查找”的定义——但是这种情况很少发生，因为不知道表示选项的服务器可能不值得调用。***

***你可能会问自己，“如果`/users`是我们的集合并且它存在，那么`/users?name=spock`也会存在，对吗？查询字符串在这里重要吗？”绝对的！ [RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986) 将[查询](https://datatracker.ietf.org/doc/html/rfc3986#section-3.4)组件定义为“与路径组件中的数据([第 3.3 节](https://datatracker.ietf.org/doc/html/rfc3986#section-3.3))**一起用于标识资源** …【强调矿】。”***

***对于`/users?name=spock`是单个用户的情况，一个`404`是完美的。用户不存在，因此资源也不存在，因此无法表示它。***

***我相信有些人把这个身份代码看得太重了。在收集模式中，当接收到一个请求时，服务器可能会使用给定的标准查询数据库中的一个表，但什么也没有找到。也就是物品是*没有找到*，那么自然响应状态应该是`404`吧？不完全是。HTTP 不关心服务器的底层架构。***

# ***结论***

***TL；DR:如果`/users?name=spock`是一个系列，就选`200`，如果是一个单品，就选`404`。***

***我看到的选项 [2](https://dillonredding.github.io/2021/06/28/the-empty-search-result-anti-pattern.html#option-2-204) 和 [3](https://dillonredding.github.io/2021/06/28/the-empty-search-result-anti-pattern.html#option-3-404) 的问题可以归结为混合语义。也就是说，协议被用来(和滥用)交流应用程序或领域语义。HTTP 不关心“收藏”或“搜索结果”之类的东西。它不关心各种组件背后的架构。它关心资源和消息。我们仍然可以在遵守协议的同时交流我们的应用程序语义，小心地[分离那些关注点](https://en.wikipedia.org/wiki/Separation_of_concerns)。***

***感谢阅读！欢迎随时在 Twitter [@dillon_redding](https://twitter.com/dillon_redding) 上向我提出问题或评论。***