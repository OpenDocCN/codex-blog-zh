# 使用滚动发布 Linux 发行版的 5 个理由

> 原文：<https://medium.com/codex/5-reasons-to-use-a-rolling-release-linux-distribution-a4da534ac1cc?source=collection_archive---------7----------------------->

![](img/c3f0aa8d5741860fa82d52c91ba5fa74.png)

为胜利而战！

Linux 用户可以在两种桌面发行版之间进行选择:

1.  固定(或标准)发布系统，提供安全更新，但通常将应用程序更新推迟到下一版本发布；或者
2.  滚动发布系统，定期更新所有软件包，因此，在正常情况下，永远不需要重新安装或版本升级。

虽然许多 Linux 用户只想在他们的系统上完成工作，并不关心他们软件的年龄，但是有几个很好的理由选择滚动发布版本作为他们的主要生产机器。

# 1.尖端应用

能接触到一个人最喜欢的程序的最新版本会让滚动发布的用户笑逐颜开。

大约 25 年前，当我第一次开始使用 [Linux](https://opensource.com/resources/linux) 时，我在[稳定分支](https://wiki.debian.org/DebianStable)上使用了 [Debian](https://www.debian.org/) ，其中一件一直困扰我的事情是必须等待一两年才能获得过时应用程序的新版本。随着时间的推移，我会读到新的和改进的应用程序版本，总是可以在专家级的滚动发布版本中获得，如 [Arch](https://archlinux.org/) 或 [Gentoo](https://www.gentoo.org/) ，但通常是在很久以后的 [Debian](https://www.debian.org/) 、 [Slackware](http://www.slackware.com/) 或 [OpenSUSE](https://www.opensuse.org/) (在 [Tumbleweed](https://www.opensuse.org/#Tumbleweed) 出现之前)。我[从一个固定发行版跳到另一个](https://embeddedinventor.com/distro-hopping-what-why-how-explained/)发行版，直到 2012 年，我发现了基于滚动发行版 [Arch Linux](https://archlinux.org/) 的 [Manjaro](https://manjaro.org/) ，而不必[从头安装 Arch](https://wiki.archlinux.org/title/installation_guide)，那时我发现了定期运行最新软件的感觉。从那以后，我一直使用滚动释放系统。

# 2.最新内核

如果一个 Linux 用户拥有一台新的笔记本电脑或台式电脑，使用一个最新的 Linux 内核可以决定是能够安装和运行 Linux 还是必须坚持使用硬件自带的专有操作系统(几乎总是微软的 Windows 或者苹果的 Mac)。

每一个后续的内核版本都引入了新的特性(包括[驱动](https://www.linux.org/threads/the-linux-kernel-drivers.8680/)),将功能嵌入到基本系统中。这使得下游开发人员不再需要创建和维护单独的包。每一个新的内核也放弃了应用于早已过时的硬件的功能，但现在却代表了不必要的膨胀。

对于滚动发布的发行版，对最新内核的访问通常是自动的。有了固定版本的发行版，通常可以安装新的内核，但是也有可能导致系统不稳定。对于大多数发行版，无论是固定的还是滚动的，用户都可以保留多个内核版本(通常是两个或三个)，这样，如果其中一个出现故障，用户可以引导到以前的版本。

# 3.早期接触桌面环境创新

像 [Gnome](https://www.gnome.org/) (我的首选) [KDE](https://kde.org/) ， [Deepin](https://wiki.archlinux.org/title/Deepin_Desktop_Environment) ， [Cinnamon](https://wiki.archlinux.org/title/cinnamon) ， [Budgie](https://wiki.archlinux.org/title/budgie) ， [Mate](https://wiki.archlinux.org/title/MATE) 和 [LXQt](https://wiki.archlinux.org/title/LXQt) 这样积极开发的桌面环境会定期发布版本更新，这些版本更新通常会解决 bug 并带来新功能。诚然，在最初发布时，一个新的 DE 有时会引入令人烦恼的回归，这些错误通常会在随后的点发布中得到解决。对于许多用户来说，为了享受所有的新功能，解决一些小问题(比如 Gnome [扩展不兼容](https://www.reddit.com/r/gnome/comments/pvvku5/why_do_extensions_break_with_every_gnome_version/))是值得的。

在我写这篇文章的时候(2022 年 3 月的最后几天)，我的滚动发布 OpenSUSE Tumbleweed 安装在一周前升级到了 Gnome 42，几天来我一直在体验它的新[黑暗模式、屏幕截图 UI 和其他好东西](https://www.makeuseof.com/gnome-42-released-enhanced-dark-mode-new-apps/)。另一方面，我的滚动发布的 Manjaro/Arch 和 Debian Sid 系统仍然在 Gnome 41 上，固定发布的 Fedora 和 Ubuntu 将在两到四周内发布 Gnome 42 的新版本。对我来说，通过 Tumbleweed 早期访问新的 Gnome 版本已经导致了零扩展不兼容，我对最新 Gnome 的渴望比几乎任何人都要早。

# 4.不需要系统升级

简而言之，有了滚动发布的发行版，系统会不断升级，并且不需要每 6、9、12 或 24 个月进行一次大规模的发布升级。由于发布升级是几乎所有我的固定发布系统崩溃的原因，能够消除这种痛苦的仪式是我选择滚动发布的一个巨大原因。

# 5.可以像固定发行版一样稳定

传统观点认为，像 Debian、Ubuntu 和 OpenSUSE Leap 这样的固定发布版本可以获得坚如磐石的稳定性，而像 Arch、OpenSUSE Tumbleweed 和 Debian Sid 这样的滚动发布版本天生就不稳定，因为它们总是推出没有像固定发布版本那样经过全面测试的新版本包。

事实是，Arch、Tumbleweed、Debian“不稳定”(Sid)和大多数其他滚动发布的发行版在发布到主[库](https://frameboxxindore.com/linux/what-are-repositories-in-linux.html)之前都会经过某种测试或评估阶段。在 Manjaro 的例子中，它提供了三个独立的存储库通道(稳定、测试和不稳定)，不稳定几乎等同于 Arch 主流，所以当 Arch 包到达 Manjaro 的稳定通道时，它们已经像许多固定发布版一样经过了彻底的测试。我已经运行两个不同的 Debian Sid 安装各两年多了，从来没有发生过不可挽回地破坏我的系统的事情——尽管，公平地说，有时我需要提高我的学习曲线，以便修复最终出现的某些问题。在很大程度上，更新来了又去，我能够继续工作，没有故障。

滚动发布版是为新软件包的稳定流动而设计的，随着越来越多的不稳定问题的解决方案不断推出，许多几年前常见的漏洞已经被消除。即使偶尔出现小问题，我也几乎总能在互联网上搜索修复方法，而且几乎从来不会面临重新安装系统的问题。当我安装一个固定发行版时，不可避免地会有一次更新或升级会破坏我的系统，我必须经历一个耗时的重新安装过程。

# 结论

很明显，当谈到运行 Linux 时，我更喜欢滚动发布而不是固定发布。我想使用最新的软件，我想避免经常破坏我的系统的版本升级。久而久之和我对 Linux 有了更多的了解，我意识到滚动发布可以和固定发布一样稳定(或者比固定发布更稳定)，只要我注意论坛和电子邮件列表上的更新通知，并且我愿意在互联网上搜索小问题的解决方案。我参与 Linux 社区越多，我就越有信心解决所有发行版出现的不可避免的问题。

也就是说，如果您是 Linux 新手，并且一直在使用像 Ubuntu 或 Mint 这样的入门级发行版，您可能想尝试一下滚动发布发行版。有可能你再也回不去了。

# 滚动发放分配的示例

如果您是 Linux 新手，并且想尝试滚动发行版，这里有一个例子列表。我曾经尝试过所有的方法。下面列出了基本分布和导数分布。我首先推荐 Manjaro，但最终你可能会想尝试其他一些。

[arch Linux](https://archlinux.org/)

> 曼哈罗
> 
> [心爱的人](https://endeavouros.com/)
> 
> [哥鲁达](https://garudalinux.org/)

[**Gentoo**](https://www.gentoo.org/)

> [Redcore](https://redcorelinux.org/)
> 
> [计算](https://www.calculate-linux.org/)
> 
> 萨巴永

[](https://get.opensuse.org/tumbleweed/)

> **[壁虎滚动](https://geckolinux.github.io/)**

**[**【Debian 不稳定(Sid)**](https://wiki.debian.org/DebianUnstable) **或** [**测试**](https://wiki.debian.org/DebianTesting)**

> **[指导](https://siduction.org/)**
> 
> **[火花滚动](https://sparkylinux.org/download/rolling/)**