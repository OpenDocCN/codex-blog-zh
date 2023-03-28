# 关于 Log4perl 的一个简要说明

> 原文：<https://medium.com/codex/a-brief-note-on-log4perl-6d19431cb0a?source=collection_archive---------3----------------------->

![](img/ae14cd6bc7eb7bfd12a2c27ed729f16d.png)

*Photo by Pixabay 上*[T3【Pexels.com】](https://www.pexels.com/photo/man-cutting-tress-using-chainsaw-209229/)

当安全研究人员在 12 月 9 日披露了流行的 Apache Log4j 2 软件库中有一个用于记录和调试事件的漏洞时，Java 世界度过了一个有趣的周末。各种各样的系统，如[亚马逊网络服务](https://aws.amazon.com/security/security-bulletins/AWS-2021-006/)、[苹果 iCloud](https://www.macworld.com/article/559108/icloud-patch-log4shell-exploit.html) 和[《我的世界》视频游戏](https://www.minecraft.net/en-us/article/important-message--security-vulnerability-java-edition)都可以被利用，只需通过发送特制的文本字符串就可以在服务器上运行任意代码。信息技术经济专业公司…