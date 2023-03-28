# iOS 访谈:依赖注入[类型和实现]

> 原文：<https://medium.com/codex/ios-interview-dependency-injection-types-and-implementation-a216d182f047?source=collection_archive---------4----------------------->

当你开始面试一份 iOS 开发人员的工作时，你曾经面临过这些问题吗？

![](img/b982ae832961d742d957d261f8f420da.png)

国防情报部

> 1.什么是依赖注入？
> 
> 2.依赖注入的好处是什么？
> 
> 3.依赖注入有哪些类型？

令人惊讶的是，你在代码中的某个地方使用了依赖注入，但是你无法用这个概念来解释它。

所以让我们开始理解依赖注入。

依赖注入是一种软件设计模式，在这种模式中，一个对象接收它所依赖的其他实例。

所以问题来了，如果通过创建一个类的对象，然后使用属性和函数，一切都很好，为什么我们需要依赖注入？其背后的原因是，依赖注入赋予了编写更多可测试、可重用代码的能力，这有助于我们编写可维护的软件，我们可以用模拟数据执行测试，这将简化测试。它是一种设计模式，涉及为组件提供其依赖关系，而不是让组件自己创建或定位它们。这可能是有用的，原因有很多，包括:

*   *它使得隔离测试组件变得更加容易，因为您可以注入模拟依赖项，而不必设置真正的依赖项。*
*   *它允许你将组件彼此分离，使得重用和维护它们变得更加容易。*
*   *它可以使管理复杂系统变得更容易，因为您可以在一个中心位置定义依赖关系，而不是将它们分散在代码库中。*

根据您的应用需求，在 Swift 中实施依赖注入有多种方式。一些常见的方法包括:
**1。构造函数注入**:这涉及到将依赖项作为参数传递给组件的初始化器。下面是构造函数注入的例子。

```
class NetworkClient {
  func performRequest(url: URL) {
    // Perform network request
  }
}

class UserService {
  let networkClient: NetworkClient

  init(networkClient: NetworkClient) {
    self.networkClient = networkClient
  }

  func fetchUsers() {
    let url = URL(string: "url")!
    networkClient.performRequest(url: url)
  }
}

let networkClient = NetworkClient()
let userService = UserService(networkClient: networkClient)
userService.fetchUsers()
```

在上面的例子中，`UserService`依赖于`NetworkClient`来执行网络请求。通过初始化器将`NetworkClient`注入`UserService`，然后`UserService`可以使用`NetworkClient`执行请求。

**2。属性注入:**这包括在组件被初始化后，将组件的依赖项设置为属性。

```
class UserService {
  var networkClient: NetworkClient?

  func fetchUsers() {
    let url = URL(string: "url")!
    networkClient?.performRequest(url: url)
  }
}

let networkClient = NetworkClient()
let userService = UserService()
userService.networkClient = networkClient
userService.fetchUsers()
```

**3。方法注入:**这包括将依赖项作为参数传递给需要它们的方法。

```
class UserService {
  func fetchUsers(using networkClient: NetworkClient) {
    let url = URL(string: "url")!
    networkClient.performRequest(url: url)
  }
}

let networkClient = NetworkClient()
let userService = UserService()
userService.fetchUsers(using: networkClient)
```

**4。接口注入:**这包括为依赖关系定义一个接口，并让组件实现这个接口。当组件需要使用具有相似角色但不同实现的多个依赖项时，这可能很有用。例如:

```
protocol NetworkClient {
  func performRequest(url: URL)
}

class HTTPNetworkClient: NetworkClient {
  func performRequest(url: URL) {
    // Perform network request using HTTP
  }
}

class UserService {
  var networkClient: NetworkClient

  init(networkClient: NetworkClient) {
    self.networkClient = networkClient
  }

  func fetchUsers() {
    let url = URL(string: "url")!
    networkClient.performRequest(url: url)
  }
}

let networkClient = HTTPNetworkClient()
let userService = UserService(networkClient: networkClient)
userService.fetchUsers()
```

***如果您喜欢这个，点击💚尽你所能在下面为这篇文章鼓掌，这样其他人会在媒体上看到。如果您有任何疑问或建议，请随时评论或点击***[***Twitter***](https://twitter.com/b_banzara)***，或***[***Linkedin***](https://www.linkedin.com/in/rranjanchchn/)