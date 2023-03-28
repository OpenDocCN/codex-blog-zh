# 在启用接收段合并的 Windows Server 2019 上加载文件延迟！

> 原文：<https://medium.com/codex/load-files-delayed-on-windows-server-2019-with-receive-segment-coalescing-enabled-1b416b3ffaef?source=collection_archive---------2----------------------->

## 缓慢加载-包在一个 Delphi 应用程序中！

![](img/405f7734b533f45a91bee941cff1b675.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

我最近有机会研究并解决了一个缓慢加载库(DLL，BPL 包等)的问题。..)组成一个用 **Delphi** RAD Studio 编写的应用程序！在将应用程序所在的**虚拟机**的操作系统更新到 **Windows Server 2019** 后，用户立即报告了该问题。特别是在启动可执行文件和显示第一个表单(登录表单)之间会报告速度变慢。

事情是这样的:

1.  用户启动应用程序
2.  应用程序启动，相关的进程开始分配内存(您可以从任务管理器中监视它)，但是不显示任何表单
3.  将显示登录表单

第二步的持续时间在 60 到 90 秒之间。仅当应用程序从**网络路径**运行时，问题才会出现。

## 执行第一次检查

已检查操作系统和防病毒设置。已卸载防病毒软件(非 Microsoft)以删除过滤器驱动程序，在某些情况下，过滤器驱动程序被认为是通过网络访问文件时降低性能的一个因素。有关过滤器驱动程序的有用信息可以在以下文章中找到:

*   [当您处理位于文件服务器上的文件时，系统停止响应、文件服务器性能降低或出现延迟](https://docs.microsoft.com/en-us/troubleshoot/windows-server/performance/slow-performance-file-server)
*   [如何在 Windows 中临时停用内核态过滤驱动](https://docs.microsoft.com/en-us/troubleshoot/windows-server/performance/deactivate-kernel-mode-filter-driver)
*   [如何提高 Windows 10 中从远程共享文件夹打开文件时的性能](https://www.techrepublic.com/article/how-to-improve-performance-when-opening-a-file-from-a-remote-shared-folder-in-windows-10/)

服务器和客户端已更新至 Windows Server 2019 和 Windows 10 的最新版本。已经检查了网络，执行了性能测试，但是系统检查没有任何结果。矛头指向了应用程序，它在软件模块(BPL)和组成它的库的加载阶段变得很慢。我们还认为我们遇到了 Marco Cantù在这篇文章中描述的问题:[Delphi 运行时包和 Windows 10 Creators 更新的问题](https://community.embarcadero.com/de/blogs/entry/the-issue-with-delphi-runtime-packages-and-windows-10-creators-update)。

对应用程序的加载系统进行了深入分析，创建了一个测试应用程序以在一个简化的场景中重现问题，由于这最后一个测试，我们开始认为这不是应用程序问题，而是基础架构或操作系统的问题。

即使没有应用程序，也可以重现该问题，只需以这种方式使用 Zip 文件:在 2019 Hyper-V 主机上创建一个 Windows Server 2019 VM。在 2019 虚拟机中的任何驱动器/共享上放置一个 Zip 文件，并尝试直接从 Windows 客户端操作系统(在 Windows 10 Pro 21 h1 Build 19043.1288 上测试)中提取网络位置的内容，解压缩将非常缓慢，并可能失败。使用 Windows Server 2016 作为客户端进行相同的测试没有问题。

## 解决方案

此问题已通过禁用主机系统(Hyper-V)的 vSwitch 中的[接收段合并(RSC)](https://docs.microsoft.com/sl-si/windows-server/networking/technologies/hpn/rsc-in-the-vswitch?view=vsts) 得到修复，如本[线程](https://social.technet.microsoft.com/Forums/en-US/8aa6a88c-ffc8-4ede-abfc-42e746ff5996/windows-server-2019-hyperv-guest-on-windows-server-2019-hyperv-host?forum=winserverhyperv&prof=required)所建议。

禁用接收段合并(RSC)大大减少了加载应用程序库所需的时间，在这种情况下，对于具有几十个软件库的复杂应用程序来说，这种优势是显著的。网上的搜索也突出了 Windows server 2019 上的 VMWare 的相同问题！

希望有所帮助！