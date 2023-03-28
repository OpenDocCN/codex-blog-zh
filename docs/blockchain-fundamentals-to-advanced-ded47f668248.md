# 区块链基础到高级

> 原文：<https://medium.com/codex/blockchain-fundamentals-to-advanced-ded47f668248?source=collection_archive---------11----------------------->

## n 的系列 1

在这篇文章中，我们将通过流行词区块链和共识算法的需要。

**那么什么是区块链呢？**

![](img/cc33ad913a886857632fd5f2dec65585.png)

照片由 [Launchpresso](https://unsplash.com/@launchpresso?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

区块链是一个数据库，存储网络中节点的交易日志。区块链中的“块”是指存储。块具有有限的存储容量，一旦达到一个块的全部容量，该块将被附加到前一个块，形成一个链。因此得名“区块链”。

区块链是一个不断增长的以加密方式连接的区块列表。区块链的目的是提供存储和分发数字信息而不编辑它的许可。区块链中的块由块号、数据、随机数、前一个块的散列组成。NONCE 是块中唯一可变的元素。

每个块都有前一个块的加密哈希，对于每个事务/记录，都会生成一个哈希。SHA-256 就是这样一种散列算法。哈希算法以文本或数字的形式获得任意输入，输出是固定长度的加密数据。事务中的一个小变化将导致不同的散列值。因此，我们可以确定在哪个节点/块编辑数据。

例如:-

输入:-

1.  足球和篮球
2.  足球和篮球
3.  足球和篮球

哈希:-

1.  c 4d 86 bea 999 b 76 e 464 ba 521181 CCB 07 ffd 7 f 59244 a 280883923 F5 ad 03 b 045 c 57
2.  c 4d 86 bea 999 b 76 e 464 ba 521181 CCB 07 ffd 7 f 59244 a 280883923 F5 ad 03 b 045 c 57
3.  0 DFD 2 e 9662 FB 611 E4 ecbe 3 b 270 e 699 b 63 cf 7d 9 e 3 b 8 f 046 e 78 AE 0 a3 e 401 cdba 92

我们可以观察到在“Basketball”到“Basketball”的拼写上的一个小变化导致了完全不同的散列。

注意:-请通过:1)不可变的分类帐 2)分布式对等网络。我在下面的信贷部分附上了一些链接。

在进入共识算法之前，我们需要知道什么是共识，为什么我们需要共识算法。一些共识算法是工作证明、利害关系证明、委托利害关系证明、权威证明、经过时间证明。

协商一致意味着所有各方之间的普遍一致。在区块链，共识是最重要的。

![](img/892b05338c1c0fda6950f3ad9f42aa67.png)

**为什么我们需要共识？**

假设一个区块链的长度为 100，黑客试图操纵 5 号块中的数据。一旦数据被操纵，该块的散列被完全改变，具有块 5 的先前散列的下一个块不同于生成的新散列，因此块之间的加密链接被破坏。黑客必须操纵和改变其他剩余块中的所有数据，以确保加密链接没有被破坏。即使黑客已经成功地同时操纵了所有块，分布式对等网络所做的定期检查也将恢复旧数据。这就是共识所在，假设网络由 100 个系统组成，所有这 100 个系统都由区块链的相同副本组成。如果黑客已经成功地操纵了 100 个系统中的一个，网络的定期检查将检查所有系统中的所有散列，如果散列与大多数系统的散列不同，则网络将知道系统有故障，并且新数据被移除，旧数据被恢复。如果一个黑客成功地操纵了 100 个系统中的 30 个，即使如此，大多数也是 70%,因此旧数据被恢复。如果黑客已经操纵了 100 个系统中的 51 个，那么网络将把错误的数据作为合法的数据。共识是多数投票(大多数系统同意该数据是合法的)。但是这种情况实际上是不可能的，因为在几秒钟内操纵超过一半的系统是不可能的。

![](img/dc913c873c990fecbe6aef37bc564e59.png)

安德烈·弗朗索瓦·麦肯齐在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

在这篇文章的高潮，我们现在有一些区块链的基本知识。在下一篇文章中，我们将讨论工作一致性算法的证明。我希望这篇文章对你有用，非常感谢你的反馈。感谢您的阅读。

请务必阅读我的其他关于 AI/ML 的文章:-【https://medium.com/@shreenidhihipparagi 

# 信贷科

注意信用:-

1.  不可变账本:[https://www . solulab . com/what-is-Immutable-Ledger-in-区块链-及其收益/#:~:text = Immutable % 20 Ledger % 20 in % 20 block chain % 20 reference，the % 20 security % 20 is % 20 quite % 20 night](https://www.solulab.com/what-is-immutable-ledger-in-blockchain-and-its-benefits/#:~:text=Immutable%20ledger%20in%20blockchain%20refers,the%20security%20is%20quite%20tight)。
2.  分布式对等网络:[https://www . block chain-Council . org/区块链/区块链-p2p-network/](https://www.blockchain-council.org/blockchain/blockchain-role-of-p2p-network/)

图片致谢:-

1.  封面照片:[发布会](https://unsplash.com/@launchpresso?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片
2.  协议照片:照片由 [Pexels](https://www.pexels.com/photo/two-person-shake-hands-2058140/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Oleg Magni](https://www.pexels.com/@oleg-magni?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄
3.  比特币照片:andréFran ois McKenzie 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片