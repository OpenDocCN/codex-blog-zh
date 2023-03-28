# 量子计算对网络安全的威胁

> 原文：<https://medium.com/codex/quantum-computing-threat-to-cybersecurity-243c16a6ec0f?source=collection_archive---------11----------------------->

![](img/ae4b6eed931d78061a236a4f548b56fb.png)

量子计算

两种技术之间正在进行一场史诗般的战斗。第一种是已经存在了大约 70 年的经典计算技术，目前是这两种技术中最强大和最有用的。虽然经典计算这些年来有了突飞猛进的改进，但进一步的发展变得越来越困难，因为它突破了物理学的极限。第二个是已经存在了大约 20 年的**量子计算技术**。量子计算的潜力如此之大，技术如此之新。我们只是触及了这项技术的表面，量子计算机成为解决问题的最佳超级计算机只是时间问题。

**术语**

```
**Quantum Computers** are devices that perform quantum information processing using the physical reality that quantum theory tells us about for the purposes of performing tasks that were previously thought impossible or infeasible.**Quantum supremacy** is when a quantum computer is able to perform a task a conventional computer can’t do.**Qubits** are a unit of information that quantum computers use to perform tasks.**Symmetric** algorithm can also be called a secret key algorithm, which uses only one key, a secret key for encryption and decryption of messages.**Asymmetric** algorithm can also be called public-key cryptography, which uses private and public keys to encrypt and decrypt the data.
```

## 经典计算机 Vs 量子计算机

经典计算机使用位，它们的运算基于两个位置中的一个。单个状态，如开或关、1 或 0，称为一个位。量子计算机使用量子位，可以是其中之一，也可以是两者同时存在。这种量子现象被称为叠加，这使得它的处理速度大大提高。

所以，从经典计算机中取出两位。这些比特可以处于四种可能的状态， ***00、01、10 或 11*** ，经典计算机需要堆叠数量惊人的比特来提高处理能力。今天最好的超级计算机有几十万亿比特，但对于量子计算机来说，信息的基本单位是一个量子比特或量子比特。两个量子位可以代表 ***00、01、10 或 11*** 四种相同的状态，但也可以同时代表所有四种状态。

量子位极易受到环境的干扰，这使得量子位和量子位操作(所谓的量子门)都极易出错。

## 量子计算的大玩家

尽管量子计算机仍处于起步阶段，但有各种各样的参与者在研究和创新量子计算。一些大型科技公司、研究所和初创公司正在为量子霸权展开竞赛。

**谷歌量子人工智能实验室**

谷歌 AI Quantum 正在通过开发量子处理器和新颖的量子算法来推进量子计算，以帮助研究人员和开发人员解决理论和实践方面的近期问题。该公司认为量子计算将有助于开发未来的创新，包括人工智能。这就是为什么它今天致力于构建专用的量子硬件和软件。量子计算是一种新的范式，将在加速人工智能的任务方面发挥重要作用。它为研究人员和开发人员提供了对开源框架和计算能力的访问，这些能力可以超越传统能力。谷歌的目标是在 2020 年前建造一台“有用的、纠错的量子计算机”。该公司[在一篇博文](https://blog.google/technology/ai/unveiling-our-new-quantum-ai-campus/)中解释道。

**IBM**

IBM Quantum 是值得信赖的合作伙伴，帮助您开始您的量子之旅，并为量子优势时代做好准备。您可以发现早期用例，为您的组织配备实用的量子技能，并获得世界级的专业知识和技术，以推进量子计算领域。它是你最热情的合作者，推进将产生现实世界影响的基础量子计算研究。与实验、理论和计算机科学领域的最佳专家合作，探索量子计算领域的新可能性。

**微软**

微软采用全面的方法来提供所有需要的技术，以实现 quantum 从开发到部署的商业影响。这种方法在计算堆栈的所有层并行创新，包括控制、软件、开发工具和服务。它还包括一个主要的持续重点，即开发拓扑量子位，以帮助实现可扩展的稳定的量子计算。

**里盖蒂计算**

Rigetti 计算机预计将于 2022 年第一季度上市。该公司表示，这将是互联网上最大的量子计算机。Rigetti 量子计算机使用超导量子位，它声称与其他商业可用的量子计算技术相比，它具有更快的执行时间和更大的规模。

## 量子计算的应用

量子计算机的理论潜力是巨大而广泛的。与经典计算机相比，许多领域可以从以完全不同的方式解决问题的计算优势中受益。下面是量子计算机的一些应用。

*   网络安全
*   人工智能
*   交通优化
*   药物开发
*   电子材料发现
*   金融建模
*   更好的电池
*   天气预报和气候变化

## 网络安全的量子威胁与后量子密码

量子计算的到来将导致加密策略的调整。目前，最广泛使用的非对称算法主要基于困难的数学问题，包括分解大数，这在当今最有效的超级计算机上可能需要很多年。

然而，过去二十多年来麻省理工学院的彼得·肖尔(Peter Shor)所做的研究表明，在大规模量子计算机上，等式问题理论上应该在几天或几小时内解决。未来的量子计算机可能有能力破解基于整数因子分解或离散对数的非对称加密方案。

尽管对称算法不受[肖尔算法、](https://en.wikipedia.org/wiki/Shor%27s_algorithm)量子计算能力的影响，但后者需要增加密钥大小。例如，运行[格罗弗算法](https://en.wikipedia.org/wiki/Grover%27s_algorithm)的大型量子计算机，使用量子标准立即查看数据库，应该在对对称加密算法(如 AES)的暴力攻击方面提供二次发展。

为了帮助抵御暴力攻击，密钥大小需要加倍以提供相同级别的保护。对于 AES，这意味着使用 256 位密钥来保持今天的 128 位安全强度。

## 结论

量子计算机将会非常强大，并且有潜力提供巨大的社会效益。然而，我们的对手、竞争对手或罪犯可能会利用量子计算机，这也带来了一些威胁。例如，如今恶意实体可以捕获感兴趣的通信。然后，当量子计算机可用时，这种强大的计算能力可以用来破解加密并找出那些通信。

## 本文最初发表于 [**赛博枢纽情报博客**](https://cyberhubintelligence.com/quantum-computing-threat-to-cybersecurity/)