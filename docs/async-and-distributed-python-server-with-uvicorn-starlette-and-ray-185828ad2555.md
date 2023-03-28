# 带有 Uvicorn、Starlette 和 Ray 的异步分布式 Python 服务器

> 原文：<https://medium.com/codex/async-and-distributed-python-server-with-uvicorn-starlette-and-ray-185828ad2555?source=collection_archive---------6----------------------->

![](img/7ac830baad6b0df190c0d3af72449f97.png)

Python 目前在数据科学领域占据主导地位，因此许多公司都在用它构建服务器应用程序。 ***异步 Python*******uvicon*******Starlette****和****Ray****是创建可伸缩的分布式服务器应用的绝佳机会。***

## **介绍**

**作为一名系统架构师，我经常担心我们所做的是否是“可伸缩的”。但这意味着什么呢？可伸缩性有许多方面，它高度依赖于应用程序环境。例如，与服务静态网站相比，移动应用的后端可伸缩性有着非常不同的要求。我所关心的那种应用**

*   **通常基于网络；**
*   **有几个用户；**
*   **并且经常处理数据。**

**数据处理的后端通常是用 Python 而不是更快的 Java 编写的。**

**三年前，我去了伦敦的 PyCon。我震惊地发现，包括银行在内的许多组织都在运行用 Python 编写的服务器应用程序。很公平，银行没有用它们来进行纳秒级的高频交易，但我一直认为这些家伙只使用 Java 或者更快的东西，比如 C++。**

**如今，我们还为客户提供用 Python 编写的后端。在处理数据时，这种语言是非常宝贵的，数据科学家喜欢它。任何与数据科学家一起工作的人都理解将他们的 Python 脚本转换成可以在生产中运行的东西的痛苦。想象一下，必须把每一个剧本翻译成不同的语言，混乱就完成了。每次我这样做的时候，我发誓下次我会照原样运行脚本！**

**Python 并不是最快的选择，但当涉及到处理数据时，用户习惯于等待比加载网页更长的时间。所以至少现在，这种语言将继续主导数据处理、争论、人工智能等。**

**例如，Instagram 多年来在 Python 服务器上进行了大量投资。例如，他们发布了自己的 Python 3.8 编译，试图提高服务器的性能:**

> **Cinder 是 Instagram 内部面向性能的 CPython 3.8 量产版。([来源](https://github.com/facebookincubator/cinder))**
> 
> **Instagram 服务器是一个几百万行的 Python 庞然大物，移动速度很快。([来源](https://instagram-engineering.com/python-at-scale-strict-modules-c0bb9245c834))**

**好吧，但是为来自 Python 服务器的中等数量的请求提供服务怎么样呢？Python 不是单线程的吗？是的，这是一个问题。幸运的是，这个问题有一些可能的解决方案。**

## **[uv icon](https://www.uvicorn.org/)+[Starlette](https://www.starlette.io/)+async 进行加工**

**异步服务器很容易推理，它们使得从一个 CPU 中获取更多资源变得简单。当服务器等待 HTTP 请求返回时，它可以处理其他任务。就这么简单。下面是一个简单的异步 Uvicorn 服务器示例:**

```
**from starlette.applications import Starlette
from starlette.requests import Request
from starlette.responses import JSONResponse, PlainTextResponse
from starlette.routing import Routeasync def homepage(req: Request):
    return PlainTextResponse("ok: service is running")async def echo(req: Request):
    try:
        res = await req.json() except Exception as err:
        res = {"message": "failed JSON message"} return JSONResponse(res)app = Starlette(
    debug=True,
    routes=[
        Route("/", homepage, methods=["GET"]),
        Route("/echo", echo, methods=["POST"]),
    ],
)**
```

**要启动该服务器，您只需运行以下命令:**

**`uvicorn main:app --workers 2`**

**假设上面的脚本在一个名为`main.py`的文件中。**

**恭喜你。您刚刚启动了一个包含三个 Python 进程、主进程和两个 worker/children 的 HTTP 服务器。使用 worker 很有帮助，因为它们是独立的 python 进程，如果一个 worker 很忙，另一个 worker 可以处理传入的请求。Uvicorn 似乎对可以启动的工作线程数量没有限制，但是每个工作线程都是一个独立的进程，不管当前的服务器负载如何，它都会占用资源。每个进程也异步执行请求。**

**当服务器需要做一些繁重的工作时，例如处理大量数据、运行 ETL 等，这些工作人员就会出现问题。工作器是用来处理请求的，所以像 ETLs 这样长时间运行的东西是个坏主意。它们设计了一个响应分钟级请求的超时，这对请求有意义，但对更长的计算没有意义。输入 ray.io！**

## **[面向分布式计算的 Ray.io](https://ray.io/)**

**Ray 是 Python 生态系统中相对较新的成员。我认为是在 2018 年首次公开宣布。Ray 填补了一个重要的空白，即在集群中编排多个 python 进程的能力。它是专门为处理大量数据进行 ML 训练而设计的。Ray 允许您用自己的资源启动一个 actor 进程，并耐心等待结果变得可用。**

**一个合理的场景是向服务器发送请求来运行 ETL 过程。请求会立即得到“ok，ETL 正在运行”的回答，而实际的数据处理工作则交给了 Ray，用户可以稍后回来检查结果。Ray 本身可以根据需求无缝扩展。**

**这是一个简单的例子，展示了如何创建一个使用光线演员的 Uvicorn 服务器。**

```
**import ray
from starlette.applications import Starlette
from starlette.responses import JSONResponse, PlainTextResponse
from starlette.routing import Route
from starlette.requests import Request[@ray](http://twitter.com/ray).remote
class EchoActor(object):
    def __init__(self):
        passdef response(self, jsonMessage: dict):
        return JSONResponse(jsonMessage)ray.init()
ray.nodes()async def echo(req: Request): echoBot = EchoActor.remote()
    try:
        res = await req.json() except Exception as err:
        res = {"message": "failed JSON message"} remoteJob = await echoBot.response.remote(res) return remoteJobapp = Starlette(
    debug=True,
    routes=[
        Route("/echo", echo, methods=["POST"]),
    ],
)**
```

## **结论**

**计算的参与者模型及其在分布式/并行计算中的应用并不新鲜。Scala + Akka 的演员已经有 10 多年了。很高兴看到类似的概念最终在 Python 环境中可用。与 Akka 相比，一个主要的区别是光线演员的开销。在 Ray 中，它们是独立的 Python 进程，每个进程都有自己的内存和 CPU 需求。初始化一个新的光线角色需要一点时间，或者至少比 Akka 中相同的步骤要长得多。因此，上下旋转光线演员需要小心，但这仍然比从头开始创建相同的功能要容易得多(也更少错误)。相比之下，Akka 演员非常轻，足迹最小；以至于你可以轻松地在内存中运行 10，000 多个。**

**就完整性而言，Uvicorn workers 非常类似于 Ray actors，但是它们实际上是用来处理服务器请求的，而不是根据需求进行伸缩。**

**简而言之，Ray 并不完美，但它为 Python 生态系统增加了一个重要的功能，并且与 Uvicron 这样的异步服务器框架相结合，它走了很长的路！**

***我的名字叫尼克·瓦克莱夫，我是*[*Techccino Ltd*](https://techccino.com/)*的创始人。如果你有兴趣构建自己的网络应用* [*联系*](https://techccino.com) *。***