# 硒元素 Chrome 开发工具一瞥

> 原文：<https://medium.com/codex/selenium4-a-peek-into-chrome-devtools-92bca6de55e0?source=collection_archive---------2----------------------->

![](img/2fcf29727d293604f72508a5654fa57e.png)

沙哈达特·拉赫曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Selenium 4 增加了对[Chrome DevTools API](https://chromedevtools.github.io/devtools-protocol/)的原生支持。有了这些新的 API，我们现在可以监控和拦截 HTTP 请求和 HTTP 响应、模拟网络速度、基本身份验证、验证和清除应用程序 cookies 和缓存、访问控制台日志和性能指标，等等。

在开始测试之前，我们将初始化 devtools 会话和 webdriver

# ***监控 HTTP 请求***

为了监视请求，我们需要将事件 Network.requestWillBeSent 传递给侦听器。当页面将要发送 HTTP 请求时，将触发此事件。

在这之后，我们使用一个监听器监听发送的各种 HTTP 请求，它们的请求头、post 主体、URL 等。

让我们来看一些示例代码:

# **拦截 HTTP 响应**

在拦截响应之前，我们将使用 Network.responseReceived event。当 HTTP 响应可用时触发，这样我们就可以监听 URL、响应头、响应代码等。要获得响应体，请使用 Network.getResponseBody 方法

# **仿真网络速度**

许多人使用手持设备上的 web 应用程序，如连接到 wifi 或蜂窝网络的 iPads、手机。在不同的网络速度下测试应用程序是很重要的。

# 注册基本授权

使用 devtools，我们现在可以直接处理认证，为此我们需要使用方法[network . setextrahttpheaders](https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-setExtraHTTPHeaders)。

# **阻止网址**

我们可以在网页加载时阻止某些 URL 的加载。

# 验证 Cookies

有了 chrome 开发工具 API，我们可以玩浏览器 cookies。我们可以清除浏览器 cookie，删除所有 cookie，获取和设置 cookie 或 cookie。

还有其他方法也适用于 cookies:

1.  [Network.deleteCookies](https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-deleteCookies) :删除具有匹配名称和 URL 或域/路径对的浏览器 cookies。
2.  [Network.getCookies](https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-getCookies) :返回当前 URL 的所有浏览器 cookies。根据后端支持，将在 cookie 字段中返回详细的 cookie 信息。
3.  [Network.setCookie](https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-setCookie) :用给定的 Cookie 数据设置一个 cookie。
4.  [Network.setCookies](https://chromedevtools.github.io/devtools-protocol/tot/Network/#method-setCookies) :设置给定的 cookies。

# **覆盖用户代理**

有时，我们想测试移动网络应用，这就是用户代理发挥作用的地方。我们可以通过提供移动用户代理将我们的浏览器改为移动浏览器。

# 加载不安全的网站

使用 DevTools，我们可以忽略 SSL 安全警告。

# **捕获控制台日志**

当应用程序打开时，应用程序发布的控制台错误日志被捕获。

# **模拟设备尺寸**

Web 应用程序是为各种平台和设备构建的，如各种尺寸的手机、平板电脑和笔记本电脑。作为测试人员，我们应该在不同的维度上检查应用程序的响应性。

我们可以使用方法 emulation . setdevicemetricoverride 并将其传递给 devTools.send()。该方法需要 12 个参数，其中 4 个是强制的，8 个是可选的。通过使用 executeCdpCommand()，我们可以传递 4 个强制参数，忽略 8 个可选参数。

# 模拟地理位置

在很多情况下，我们需要测试某些基于位置的功能，比如优惠、基于位置的价格等。对于这些，我们可以使用 DevTools API 模拟位置。

# 性能指标

使用 DevTools，我们可以在运行自动化测试的同时获得性能指标。

这只是几个例子，Chrome DevTools API 提供了更多的工具来增强自动化测试。

*继续探索！！！*

*完整项目和更多样本测试可以在我的*[*github*](https://github.com/attrideepak/selenium-web-automation)*上找到。*