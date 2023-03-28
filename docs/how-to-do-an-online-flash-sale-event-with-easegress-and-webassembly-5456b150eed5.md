# 如何使用 Easegress 和 WebAssembly 进行在线快闪销售活动

> 原文：<https://medium.com/codex/how-to-do-an-online-flash-sale-event-with-easegress-and-webassembly-5456b150eed5?source=collection_archive---------13----------------------->

![](img/39d076e860a1bbf5c9b269be1ce20875.png)

照片由[努贝尔森·费尔南德斯](https://unsplash.com/@nublson?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

闪购是电子商务商店提供的短期折扣或促销。数量有限，这通常意味着折扣比一般的促销更高或更大。

然而，显著的折扣、有限的数量以及短时间内导致显著的高流量峰值，这通常会导致缓慢的服务、拒绝服务甚至停机。

本文说明了如何利用 [WasmHost 过滤器](https://github.com/megaease/easegress/blob/main/doc/wasmhost.md)来保护 flash 销售中的后端服务。WebAssembly 代码使用[ease gress assembly script SDK](https://github.com/megaease/easegress-assemblyscript-sdk)在 [AssemblyScript](https://www.assemblyscript.org/) 中编写。

在开始之前，我们需要介绍一下为什么我们使用 WebAssembly 的服务网关。

*   首先，Easegress 作为服务网关，更多的是负责控制逻辑。
*   其次，像闪购这样的业务逻辑是一种更加定制化的东西，可以经常改变。

使用 Javascript 或其他高级语言编写业务逻辑可以带来良好的生产率和较低的技术壁垒。利用 WebAssembly 技术，高级语言代码可以编译成 WASM，并在运行时动态加载。此外，WebAssembly 代码具有足够好的性能和安全性。因此，这种组合可以在安全性、高性能和定制扩展方面提供完美的解决方案。

# 1.入门指南

在继续之前，请确保安装了最新版本的 [Git](https://git-scm.com/) 、 [Golang](https://golang.org/) 、 [Node.js](https://nodejs.org/) 及其软件包管理器 [npm](https://www.npmjs.com/) 。具有编写和使用 TypeScript 模块(非常类似于 AssemblyScript)的基础知识者优先。

注意:默认情况下，`WasmHost`过滤器是禁用的。要启用它，您需要用下面的命令构建 Easegress:

```
$ make build_server GOTAGS=wasmhost
```

# 1.1 建立快闪销售项目

1)将 git 存储库`easegress-assemblyscript-sdk`克隆到磁盘上的某个位置

```
$ git clone [https://github.com/megaease/easegress-assemblyscript-sdk.git](https://github.com/megaease/easegress-assemblyscript-sdk.git)
```

2)切换到新目录并初始化新节点模块:

```
npm init
```

3)使用 npm 安装 AssemblyScript 编译器，假设生产中不需要该编译器，并使其成为开发依赖项:

```
npm install --save-dev assemblyscript
```

4)安装后，编译器提供了一个方便的搭建实用程序来快速建立一个新的 AssemblyScript 项目，例如，在刚刚初始化的节点模块的目录中:

```
npx asinit .
```

5)将`--use abort=`添加到`package.json`中的`asc`，例如:

```
"asbuild:untouched": "asc assembly/index.ts --target debug --use abort=",
"asbuild:optimized": "asc assembly/index.ts --target release --use abort=",
```

6)将`assembly/index.ts`的内容替换为下面的代码，注意将`{EASEGRESS_SDK_PATH}`替换为步骤 1)中的路径。该代码只是一个框架，目前“什么都不做”,以后会增强:

7)用下面的命令构建，如果一切正常，`untouched.wasm`(调试版本)和`optimized.wasm`(发布版本)会在`build`文件夹下生成。

```
$ npm run asbuild
```

# 1.2 在 Easegress 中创建 HTTP 服务器和管道

在 Easegress 中创建一个 HTTPServer，监听端口 10080 以处理 HTTP 流量:

```
$ echo '
kind: HTTPServer
name: http-server
port: 10080
keepAlive: true
https: false
rules:
- paths:
  - pathPrefix: /flashsale
    backend: flash-sale-pipeline' | egctl object create
```

创建包含`WasmHost`过滤器的管道`flash-sale-pipeline`:

```
$ echo '
name: flash-sale-pipeline
kind: HTTPPipeline
flow:
- filter: wasm
- filter: mockfilters:
- name: wasm
  kind: WasmHost
  maxConcurrency: 2
  code: /home/megaease/example/build/optimized.wasm
  timeout: 100ms
- name: mock
  kind: Mock
  rules:
  - body: "You can buy the laptop for $1 now.\n"
    code: 200' | egctl object create
```

注意用步骤 7 中生成的文件路径替换`/home/megaease/example/build/optimized.wasm`。

在上面的管道配置中，一个`Mock`过滤器被用作后端服务。实际上，您将需要一个`Proxy`过滤器来将请求转发到真正的后端。

# 1.3 验证我们做了什么

在新的控制台上执行下面的命令，如果一切正常，您应该会得到类似的结果:

```
$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale)
You can buy the laptop for $1 now.
```

# 2.在闪购开始前阻止所有请求

所有闪购促销都有一个开始时间，在此时间之前的请求应该被阻止。假设开始时间是 UTC `2021-08-08 00:00:00`，这可以通过下面的代码来实现:

构建并通知 Easegress 重新加载:

```
$ npm run asbuild
$ egctl wasm reload-code
```

`curl`闪购网址，我们会在闪购开始时间前得到`not start yet.`。

```
$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale)
not start yet.
```

# 3.随机阻止请求

在 flash 销售开始后，Easegress 应该随机阻止请求，这大大减少了发送到后端服务的请求总数，从而保护服务免受流量高峰的冲击。这种随机性还带来了另一个好处:地理位置的不同会导致延迟的不同，延迟较低的用户更有可能成为早期用户。随机性消除了这些用户的优势，使闪购更加公平。

构建并验证(假设闪购已经开始):

```
$ npm run asbuild$ egctl wasm reload-code$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale)
sold out.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale)
You can buy the laptop for $1 now.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale)
sold out.
```

我们将以 40%的可能性得到一条`sold out`消息。注意这个例子中的`blockRatio`是`0.4`，而实际上，`0.999`，`0.9999`会更有意义。

# 4.幸运一次，幸运永远

从商业的角度来看，在我们允许一个幸运的用户前进之后，我们应该总是允许这个用户前进；但是从上一步的代码逻辑来看，如果用户再次访问该 URL，请求可能会被阻止。

幸运的是，所有用户在加入闪购前都需要登录，也就是说请求中会包含一个用户的标识符，我们可以用这个标识符来记录幸运的用户。

例如，我们假设`Authorization`头的值是期望的标识符(该标识符可以是 JWT 令牌，并且[验证器过滤器](https://github.com/haoel/easegress/blob/main/doc/cookbook/security.md#security-verify-credential)可以用于验证该令牌，但是这超出了本文的范围)。

然而，由于过滤器配置中的`maxConcurrency`选项，使用`Set`存储所有允许的用户将不起作用。

`maxConcurrency`是`WasmHost`过滤器的 WebAssembly 虚拟机的数量，因为 WebAssembly 被设计为安全的，所以即使两个虚拟机正在执行相同的代码副本，它们也不能共享数据。也就是说，在 VM1 允许一个用户之后，如果该用户的下一个请求由 VM2 处理，它可能会被阻塞。当 Easegress 作为集群部署时，也可能发生这种情况。

为了解决这个问题，Easegress 提供了访问共享数据的 API:

构建并验证:

```
$ npm run asbuild$ egctl wasm reload-code$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user1
sold out.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user1
You can buy the laptop for $1 now.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user1
You can buy the laptop for $1 now.
```

重复`curl`命令，我们会发现该用户在第一次被允许后将不再被阻止。

# 5.限制允许用户的数量

由于在闪购中数量通常有限，我们可以在允许一定数量的用户后阻止用户。例如，如果数量为 10，则在大多数情况下允许 100 个用户就足够了:

构建并验证:

```
$ npm run asbuild$ egctl wasm reload-code$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user1
You can buy the laptop for $1 now.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user2
sold out.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user2
You can buy the laptop for $1 now.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user3
You can buy the laptop for $1 now.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user4
sold out.$ curl [http://127.0.0.1:10080/flashsale](http://127.0.0.1:10080/flashsale) -HAuthorization:user4
sold out.
```

3 个用户被允许后，第 4 个用户将被永远阻止。

# 6.重用程序

# 6.1 参数

在上面的例子中，我们有硬编码的`startTime`、`blockRatio`和`maxPermission`，这意味着如果我们有另一个闪购，我们需要修改代码。这不是一个好的做法。

更好的方法是将这些参数放入配置中:

```
filters:
  - name: wasm
    kind: WasmHost
    parameters:                                        # +
      startTime: "2021-08-08T00:00:00+00:00"           # +
      blockRatio: 0.4                                  # +
      maxPermission: 3                                 # +
```

然后修改程序的`constructor`，读入这些参数:

# 6.2 管理共享数据

正如我们在 **Lucky Once，Lucky Always** 中所看到的，共享数据是有用的，但是当在新的 flash 销售活动中重用代码和配置时，遗留数据可能会导致问题。Easegress 提供了管理这些数据的命令。

我们可以使用(其中`flash-sale-pipeline`是管道名称，`wasm`是过滤器名称)查看当前数据:

```
$ egctl wasm list-data flash-sale-pipeline wasm
id/user1: "true"
id/user2: "true"
id/user3: "true"
```

用以下内容更新数据:

```
$ echo '
id/user4: "true"
id/user5: "true"' | egctl wasm apply-data flash-sale-pipeline wasm$ egctl wasm list-data flash-sale-pipeline wasm
id/user1: "true"
id/user2: "true"
id/user3: "true"
id/user4: "true"
id/user5: "true"
```

并删除所有数据:

```
$ egctl wasm delete-data flash-sale-pipeline wasm
$ egctl wasm list-data flash-sale-pipeline wasm
{}
```

好了，以上都是技术细节。您可以自由地使用代码来定制您的业务逻辑。但是需要注意的是，以上只是演示，实际解决方案更复杂，因为还需要过滤爬虫和黑客。如果您需要更专业的解决方案，欢迎联系我们。

# 7.摘要

利用 WebAssembly 的安全性、高性能和实时动态加载能力，我们不仅可以在网关上进行这样的高并发业务，还可以实现一些更复杂的业务逻辑支持。因为 WebAssembly 可以复用多种高级语言(如 Javascript、C/C++、Rust、Python、C#等。).Easegress 在分布式架构的高性能流量编排中有更多的功能。这两者都给高效运维解决问题带来了很大的想象空间。