# NVIDIA mellan ox blue field-2 smart NIC 实践教程:“潜水装备”——第六部分:DPDK 性能。

> 原文：<https://medium.com/codex/nvidia-mellanox-bluefield-2-smartnic-hands-on-tutorial-rig-for-dive-b0cc4e8b2520?source=collection_archive---------3----------------------->

我如何在 Bluefield-2 SmartNIC 上启动并运行 DPDK，当使用 *pktgen* 和 *testpmd* 进行测量时，它的性能如何。

在之前的[部分](/codex/nvidia-mellanox-bluefield-2-smartnic-hands-on-tutorial-rig-for-dive-65a6b7278b23)，我们已经在 Bluefield 上安装了最新的 Ubuntu 操作系统，由[厂商自己提供](https://www.mellanox.com/eula/blue-os?mtag=bluefield_sw_drivers&mrequest=downloads&mtype=BlueField&mver=BFBs&mname=Ubuntu20.04&mfile=DOCA_v1.0_BlueField_OS_Ubuntu_20.04-5.3-1.0.0.0-3.6.0.11699-1-aarch64.bfb)。默认情况下，该版本包括 DOCA、DPDK、OvS 和所有必要的工具。因此，如果你按照[第五部分](/codex/nvidia-mellanox-bluefield-2-smartnic-hands-on-tutorial-rig-for-dive-65a6b7278b23)安装这个系统，你可以*很可能*跳过我在[第二部分博文中与 DPDK 相关的部分。](/codex/nvidia-mellanox-bluefield-2-smartnic-dpdk-rig-for-dive-part-ii-change-mode-of-operation-a994f0f0e543):)