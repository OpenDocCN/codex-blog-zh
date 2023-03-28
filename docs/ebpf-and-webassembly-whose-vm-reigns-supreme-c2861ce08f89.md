# eBPF 和 WebAssembly:谁的 VM 至高无上？

> 原文：<https://medium.com/codex/ebpf-and-webassembly-whose-vm-reigns-supreme-c2861ce08f89?source=collection_archive---------7----------------------->

## 看情况。但是对于开发者来说，Rust 是一种方式。

![](img/3c164a8c3751ad900e1ac1c2e535c692.png)

[约翰·卡梅隆](https://unsplash.com/@john_cameron?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/competition?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍照

今天在云原生世界中最热门的两个轻量级代码执行沙箱/虚拟机是 [eBPF](https://ebpf.io) 和 [WebAssembly](https://webassembly.org) 。它们都运行由 C、C++和 Rust 编译的高性能字节码程序。

然而，最大的区别是 eBPF 运行在 Linux 内核中，而 WebAssembly 运行在用户空间中。eBPF 有一些编程限制，使得它对于内核用例是安全的(例如没有无限循环)。但这也意味着 eBPF 是图灵不完全的。另一方面，WebAssembly 是一种图灵完整语言，具有打破沙箱和访问本机 OS 库的扩展。

一般来说，eBPF 适合于网络或安全相关的任务，而 WebAssembly 非常适合于业务应用程序。但肯定有重叠。

首先，已经有人努力在 [Linux 内核](https://hub.packtpub.com/introducing-wasmjit-a-kernel-mode-webassembly-runtime-for-linux/)中[运行 WebAssembly](https://link.medium.com/HAUbDlHM4ib) 。然而，他们大多不成功。对于这个用例，eBPF 是更好的选择。

然而，WebAssembly 程序可以处理许多类似内核的任务。WebAssembly 可以被 AOT 编译成本机应用程序。基于 LLVM 的云本地 WebAssembly 编译器的一个很好的例子是来自 CNCF 的 WasmEdge 运行时。本机应用程序将所有沙箱检查合并到本机库中。这使得 WebAssembly 程序的行为像一个自包含的单内核“库操作系统”。

此外，这种 AOT 编译的沙盒 WebAssembly 应用程序可以运行在微内核操作系统上，如 [seL4](https://lists.sel4.systems/hyperkitty/list/announce@sel4.systems/thread/PTIILUXXLO5X6MDAG7GJMUBYDOB7ASCJ/) ，并可以接管许多“内核级”任务。

虽然 WebAssembly 可以下降到内核级别，但是 eBPF 也可以上升到应用程序级别。例如，服务网格代理通常支持 [WebAssembly 作为扩展机制](https://github.com/proxy-wasm/spec)。因为这些代理基本上是网络应用程序，所以可以用 eBPF 编写一些扩展，以获得更高的性能和可观察性，而不会损害安全性和可扩展性。

在服务网格用例中，WebAssembly 非常适合于 [sidecar 微服务应用](https://github.com/second-state/dapr-wasm)，eBPF 适合于通过代理路由 API 流量并监控整个应用。

eBPF 和 WebAssembly 的兴起对开发者来说是个好消息。我们有更多的工具来管理和规划日益复杂的基础设施。Rust 已经成为这两种虚拟机的主要编程语言，因为它是高性能的，并且可以由 LLVM 编译成机器代码，而无需额外的运行时支持。

所以最终，开发者只需要关心 Rust API，把 Rust 程序编译成 eBPF 和 WebAssembly as。部署时需要。生锈是一种方式。