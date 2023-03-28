# 网络开发字节—爆炸性的域名和域名

> 原文：<https://medium.com/codex/web-dev-bytes-dynamite-dns-domains-2463f9fcdef1?source=collection_archive---------8----------------------->

## [抄本](http://medium.com/codex)

## 购买域名意味着什么？是时候了解一下 DNS 了！

![](img/804967fcc41d241d166e246515b10151.png)

扬·安东宁·科拉尔在 [Unsplash](https://unsplash.com/s/photos/database?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

*当我开始接触科技时，我面临的最大障碍是我没有任何互联网运作的基础知识。这些文章是我回馈给任何有抱负的 web 开发人员或寻求拓宽视野的知识者的方式。*

有时当我有一个很酷的想法时，我会去 GoDaddy 或 Google Domains 为它买一个域名。我努力让名字留在人们的脑海中，这样他们就可以很容易地访问我的网站，比如`letsunite.app`或`partymoosegames.com`(这些网站还没有完成)。如今，任何人都可以购买自己的域名，但是购买域名意味着什么呢？你得到了什么，它到底是怎么工作的？关键是**域名系统**，或者说 **DNS** 。虽然域名系统听起来很专业，但它实际上是一个非常简单的概念。

# 喂，接线员吗？

先说互联网的一个问题:和其他电脑对话。在之前的 [Web Dev Bytes](/codex/web-dev-bytes-what-is-a-website-100bcbca0b88) 中，我们谈到了互联网上的每一台计算机都有一个唯一的地址，称为 **IP 地址**。为了让您能够进行通信，您的电脑需要知道它试图访问的电脑的 IP 地址。但是当你想去网飞的时候，你不会记得它的地址是`54.165.153.56`，你也不应该想起来！简单地输入域名`netflix.com`就容易多了。

这就是 DNS 的用武之地！DNS 就像一个巨大的翻译器，知道哪个 IP 地址对应哪个域名。所以当你输入`netflix.com`时，你的浏览器实际上首先向一个 DNS 发送一个请求来找出网飞的 IP 地址是什么。然后，您的浏览器使用该 IP 地址实际连接到网飞。最后，您的浏览器可能会存储该 IP 地址以备将来使用，这样我们就不需要在全天访问网飞时每次都查询 DNS。

# DNS 老巢

所以有一个 DNS 在那里供浏览器查询，但是它在哪里呢？事实是，既然 DNS 如此简单，任何人都可以运行它！它只需要能够将名称转换成 IP 地址。当然，也有一些公司创造了可靠而快速的 DNS，所以这些是大多数人使用的。例如，谷歌在`8.8.8.8`运行一个 DNS，所以你可以配置你的电脑使用它作为它的 DNS。

因为有如此多的域名系统存在，一个问题出现了，同步新条目需要时间。同步世界各地的 DNS 可能需要很长时间！这里有一个很酷的效果:如果我的电脑配置为使用 Google 的 DNS，而你的电脑使用 Cloudflare 的 DNS，那么 Cloudflare 的 DNS 可能会先更新。这意味着在很短的一段时间内，你的网络浏览器可以在我之前访问一个新网站！

![](img/e311cb91e9443b3ad05fafb1bd884f58.png)

# 一直到顶端

现在我们了解了 DNS 的工作原理，购买域名意味着什么就更清楚了。购买`partymoosegames.com`的一部分是在 DNS 中放入一个条目，这样任何导航到那里的人都会被发送到有你网站的电脑上。但是在幕后还有更多的东西。

有一个非盈利组织叫做互联网名称与数字地址分配机构，或 ICANN。ICANN 负责与 DNS 和分配 IP 地址有关的一切事务。但是这是一项繁重的工作，所以 ICANN 与其他公司签订了合同来减轻负担。那些其他公司实质上负责顶级域名，如 T2、T3 和 T4。例如，VeriSign 是一家为`.com`网站负责**所有**IP 地址和域名的公司(事实上，他们还有很多其他的，包括`.net`和`.name`)。

所以现在威瑞信负责所有以`.com`结尾的事情。但是互联网是**巨大的**，处理所有不同语言的所有变化的域名和 IP 地址的所有请求简直太多了。所以 VeriSign 所做的是允许像 GoDaddy 这样的网站，叫做**注册商**，来处理文书工作。

![](img/d8b612f165f7ecebca57738d5be6daa1.png)

[Cytonn 摄影](https://unsplash.com/@cytonn_photography?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/handshake?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

你可以把它想象成批发商对零售商。VeriSign 是批发商，从技术上讲，你可以直接与他们开展域名业务。GoDaddy 和 Google Domains 是零售商，他们以更高的价格让这变得更容易。实际上，注册人员都在同一支股票上工作！如果我从谷歌购买一个域名，这意味着**没有人**可以从任何其他注册商那里购买那个域名！注册商之间唯一真正的区别是易用性和定价。

# 我们成功了！

哇，那是一个大字节！我们讨论了很多，从 DNS 到注册服务商。因此，下次你为你的下一个大创意购买域名时，你会知道发生了什么:

*   注册商从 VeriSign(或另一个顶级域名公司)购买域名，
*   VeriSign 更新了一个 DNS，
*   DNS 更新传播到世界各地，使您的网站可用于整个互联网！

我希望这很容易理解，请在评论中告诉我你的想法！