# 黑客嘘:错误的幽灵季节

> 原文：<https://medium.com/codex/hack-the-boo-wrong-spooky-season-3609cf3dc1fc?source=collection_archive---------24----------------------->

![](img/80fbc19924a02b981cde7ad0f31a2c9e.png)

这是我写的黑客布 CTF 的错误幽灵季法医挑战。在这个挑战中，我们被要求分析 pcap 以检测任何入侵。

![](img/baed794c9c9ee174089f2d4c6cff1a2d.png)

首先，我们下载文件并查看 pcap。观察协议层次结构，我们看到只有 TCP 流量，在它上面的更高层，我们可以看到 HTTP 流量，让我们检查一下。

![](img/f7216cf6cbe2f4fa6e47c64625da1a0e.png)

让我们使用 HTTP statistics 选项来快速查看一下发出的请求。我们可以看到几个使用 cmd 参数将命令传递给端点的请求。

![](img/e488ea22e304795da7c2d9e73d551558.png)

在其中一个请求中，我们可以看到攻击者如何试图用 socat 打开一个反向外壳。让我们更仔细地看看这个调用，然后跟踪 TCP 流。

![](img/85749dd1bfbccda670ef6c0e490e3feb.png)

通过查看这些命令，我们可以找到在将后门添加到。在/bin/bash 中的 SUID 之前，使用 Cyberchef 恢复它。

![](img/a757468e8caff2f1e9f3a056b2388e18.png)

我希望你喜欢这篇文章，并了解一些新的:D