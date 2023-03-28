# SIP 与 WebRTC 的集成:工作原理

> 原文：<https://medium.com/codex/sip-integrations-with-webrtc-how-it-works-4a4f44e2c42d?source=collection_archive---------15----------------------->

![](img/e14812f223fa17995fcae0609962bb52.png)

如果您在问这个问题，那么很有可能您已经有了 SIP 基础设施，并且正在寻找与支持 [Web 实时通信(WebRTC)](https://webrtc.org/) 的端点互联的方法，或者您已经有了 Web 应用程序，并且正在寻找与电话网络互联的方法。

在这两种情况下，目标是相同的——互联——这就是 SIP 的全部意义。

但是，在我们回答主要问题之前，我们需要清楚地了解 SIP 实际上是什么，为了做到这一点，我们需要了解一点技术。

# SIP 是什么？

[会话发起协议，简称 SIP，](https://en.wikipedia.org/wiki/Session_Initiation_Protocol)从 90 年代就已经出现了。它是一种基于文本的信令协议，用于管理两个 IP 连接的端点之间的媒体会话。它经常被用在[IP 语音(VoIP)](https://en.wikipedia.org/wiki/Voice_over_IP) 通信中，这极大地拓展了 SIP 的应用范围。最初是一个相对简单的规范，现在是一个相当大的 RFC、提案和扩展的集合。

不过，最终 SIP 的基础非常简单。其结构与 HTTP 非常相似；每条 SIP 消息由几个头部组成，每个头部占一行，后面是消息体。

SIP 消息主体使用称为[会话描述协议(SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol#:~:text=The%20Session%20Description%20Protocol%20(SDP,(VoIP)%20and%20video%20conferencing.) 的东西，它在一些 SIP 消息中用于描述关于最终将在两个端点之间流动的媒体流的信息，如流的数量和类型(例如音频、视频)以及允许什么编码(例如 711、Opus、VP8、H.264)。

在典型的呼叫建立中，将发送两个 SDP 消息——提议和应答。呼叫发起者首先发送提议，尽可能多地描述即将建立的会话以及提议方的能力/需求。然后，呼叫接收者将(如果它接受呼叫)发送回一个应答，包括在所提供的内容的上下文中关于它自己的能力/期望的信息。

如果一切顺利，这是足够双方形成连接并开始发送和接收数据的信息。

# SIP 和 WebRTC 有什么关系？

WebRTC 非常自然地与所有这些相关。像 SIP 一样，它旨在支持在两个 IP 连接的端点之间创建媒体会话。像 SIP 一样，一旦信令完成，连接使用[实时传输协议(RTP)](https://en.wikipedia.org/wiki/Real-time_Transport_Protocol) 用于媒体平面中的分组。和 SIP 一样，它使用 SDP 来描述自己。

**不同之处在于两个关键领域:**

1.  它不要求在信令平面中使用 SIP 消息。事实上，它根本没有决定使用 SIP 还是任何其他信令协议。SDP 消息的实际信令(发送/接收)有意留给应用。另一方面，SIP 定义了一种非常特定的消息格式，必须使用这种格式来封装和发送用于呼叫建立的 SDP 消息。
2.  它要求在媒体平面中使用一些 SIP 可选特征。具体来说:

*   特定编解码器的使用。音频需要 G.711 和 Opus，而视频需要 VP8 和 H.264/AVC。(请注意，从技术上讲，web 浏览器是唯一需要同时支持 VP8 和 H.264 的浏览器，但实际上，每个支持 WebRTC 的端点都应该同时考虑最大的兼容性和性能。)
*   使用[安全实时传输协议(SRTP)](https://en.wikipedia.org/wiki/Secure_Real-time_Transport_Protocol) 配置文件为媒体分组提供加密和消息认证。
*   使用[数据报传输层安全(DTLS)](https://en.wikipedia.org/wiki/Datagram_Transport_Layer_Security) 生成 SRTP 使用的密钥。DTLS 证书指纹必须在 SDP 中用信号表示。(请注意，以前支持使用安全描述(SDES ),但现在不再允许了。)
*   使用交互式连接建立、NAT 的会话遍历实用程序，以及使用 NAT 周围的中继、(ICE、STUN 和 TURN)进行网络遍历的[遍历。](https://en.wikipedia.org/wiki/Traversal_Using_Relays_around_NAT)

抛开这些差异是好是坏的争论，它们确实存在，所以我们必须与它们合作。让我们分别来看看这两者。

# 信令平面

假设现有的 SIP 基础设施不会切换到不同的信令协议(不会有太大的变化)，WebRTC 将不得不学习如何使用 SIP。

有两种方法可以实现这一点:

1.  使用 SIP 作为支持 WebRTC 的应用程序的信令栈。
2.  为支持 WebRTC 的应用程序使用另一个信令解决方案，但是要添加一个信令网关来在这个和 SIP 之间进行转换。

哪种选择更适合您，这在很大程度上取决于您现有的基础架构和扩展计划。

*   您现有 SIP 基础设施吗？
*   您是否有选择性转发单元(SFU)或多点控制单元(MCU)来帮助扩展您的 WebRTC 连接？
*   您的 web 应用程序是否已经使用了 WebSync 或 XMPP 之类的系统来进行实时文本通信？
*   您的应用程序在哪些客户端平台上运行？
*   您有运行在这些平台上的 SIP 信令栈吗？

你的正确道路将在很大程度上取决于这些问题的答案，甚至可能更多。

# 媒体平面

如果您没有现有的 SIP 基础设施，那么正确的选择可能是简单地选择被列为兼容 WebRTC 的 SIP 技术。

许多 SIP 网关(例如 FreeSWITCH)和 SIP 中继服务(例如 Voxbone)可以配置为使用 DTLS/ICE 和 WebRTC 规定的编解码器。

如果您已经有一个现有的 SIP 基础设施，那么可能有必要添加一个[会话边界控制器(SBC)](https://en.wikipedia.org/wiki/Session_border_controller) ，例如 Sonus 的 SBC 产品系列，或者可以充当 WebRTC 和 VoIP 端点之间的媒体网关的另一个类似设备，其中 SBC-to-WebRTC 分支满足 WebRTC 的要求，而 SBC-to-VoIP 分支满足 VoIP 端点的需求。

如果您有一个 [SFU/MCU 来帮助您扩展 WebRTC 连接](https://www.liveswitch.io/)，那么媒体服务器可能能够充当这个网关。

同样，对你来说正确的道路可能需要一些讨论和对你需求的评估。在开始您的 SIP 集成项目之前，[将风险最小化，让您的应用程序经得起未来考验](https://www.liveswitch.io/)。