# 使用 Solidity 通过位置 DAPP 移动应用程序退款

> 原文：<https://medium.com/codex/refund-by-location-dapp-mobile-app-using-solidity-8f82a619ee75?source=collection_archive---------8----------------------->

![](img/d7e638bd74861310e62d0ead737afb2d.png)

**简介**
简而言之，智能合约是基于区块链的算法，在满足特定标准时执行。它们通常用于自动执行协议，以便所有各方可以立即确定结论，而不需要中间人或额外的延迟。他们还可以自动化一个工作流，这样当环境
被满足时，下面的动作被执行。智能合约遵循简单的“如果/当…那么…”语句，这些语句被写入区块链的代码中。当预定条件得到满足和验证时，计算机网络执行这些动作。这些可能包括向正确的人付款、登记汽车、发送通知或开罚单。当交易完成时，区块链被更新。因此，不能修改交易，只有被授予权限的各方才能查看结果。

> 智能合约的主要好处:
> ——信任和透明:由于没有第三方的参与，参与者共享交易的加密记录，因此没有必要质疑信息是否被某个参与者为了个人利益而篡改。因为没有一项工作是在信任的基础上完成的，所以它们有时被称为无信任应用。
> -速度、效率和准确性:如果满足一个条件，合同立即执行。智能合同是数字化和自动化的，因此不需要完成文书工作，也不需要花费时间来纠正手动填写文档时经常出现的错误。安全:因为区块链的交易记录是加密的，它们难以被破解。此外，黑客需要改变整个链才能改变分布式分类账上的单个记录，因为每个记录都链接到它之前和之后的记录。

**我们的业务用例**
我们的应用程序主要用于基于雇主到雇员的场景。雇主同意向另一方(例如雇员)支付在特定地理区域停留特定时间的费用。员工的手机以一定的间隔将其 GPS 位置发送到智能合同。根据以太坊智能合同中预先约定的合同，当所有约定的条件都满足时，将执行加密货币支付。如果在任何时候，GPS 传感器指示员工在约定的 GPS 区域范围之外，则合同状态将被更新以指示其不合规。建议的用例可能有许多真实世界的应用程序。一个例子是送货系统，在这种系统中，雇主雇人送货，工人只有在到达目的地后才能得到报酬。

另一个应用领域是一个测量系统，X 公司雇佣了一家公司来收集数据。只有当雇员出现在将要进行调查的指定地点时，公司甚至代理机构才能够支付雇员。

我使用的区块链:以太坊
以太坊是一个基于区块链技术的开源分布式软件平台。它有自己的原生加密货币 Ether 和编程语言 Solidity。以太坊使用区块链网络。节点是志愿者的电脑，他们挖掘硬币，组成网络。挖掘提供了支撑货币的加密技术，而节点产生以太令牌。矿工的工资由乙醚支付，因为采矿是对计算机的资源密集型使用。

以太坊平台提供计算完整的以太坊虚拟机(EVM)。EVM 在其遍布全球的公共节点网络上运行脚本。这些节点为开发人员创建的网络分散式应用程序提供了运行所需的处理能力。开发者可以购买以太网来支付网络使用费，或者他们可以自己开采代币，成为网络的一部分。一种叫做 Gas 的内部机制决定了网络交易的价格。目前，以太坊正在使用工作证明进行验证。以太坊 2.0-也被称为宁静的 ETH 2.0，将是以太坊的一个重大更新，据说将于今年发布，据信它将使用股权证明作为其协议。

**以太坊组件** ●节点:有负责写以太坊网络中已经发生的所有
交易的挖掘节点，有实现智能合约的 EVM 节点。
为了确保以太坊网络上的所有参与者保持同步状态，并就交易的精确历史达成一致，我们将交易分批成块。这意味着几十(或几百)个事务同时被提交、同意和同步。

![](img/7623d2657e6b81d849228fc2432cd747.png)

●以太:这是一种用于以太坊网络的加密货币，就像比特币用于区块链网络一样。它是一种点对点的货币，类似于比特币。它跟踪并促进网络中的每一笔交易。
●气:是以太坊网络的一种内部货币。我们需要气体来运行以太坊网络上的应用程序，就像我们需要气体来驱动车辆一样。
●以太坊账户:有两种类型的账户。第一个是外部拥有的帐户，用于存储交易，第二个是合同帐户，用于存储智能合同的详细信息。
● Nounce:根据账户类型有不同的含义。对于外部拥有的账户，nonce 表示通过该账户的交易次数。对于合约账户，nonce 表示通过该账户生成的合约数量。
●存储根:是 Merkle 树的主根节点。账户的所有细节都存储在这里。Merkle 树的根是所有交易的验证。
● Ethash:是以太坊 1.0 中使用的 POW 算法。

**以太坊中的网络**
网络是不同的以太坊环境，您可以访问它们进行开发、测试或生产用例。由于以太坊是一种协议，所以可以有多个独立的“网络”符合该协议，而不会相互交互。不同类型的网络有:
●公共网络:世界上任何有互联网连接的人都可以访问。任何人都可以在公共区块链上读取或创建事务，并验证正在执行的事务。对等体之间的共识决定了事务的包含和网络的状态。以太坊主网:主网是主要的公共以太坊生产区块链，实际价值交易发生在分布式分类账上。在 mainnet 上进行交易需要一个真正的以太坊，并且要花费一些钱。
●以太坊测试网(Ethereum Testnets):这些网络由协议开发人员或智能合约开发人员使用，在部署到 Mainnet 之前，在类似生产的环境中测试协议升级和潜在的智能合约。可以把这看作是生产服务器和登台服务器的类比。测试网上的 ETH 没有实际价值；因此，testnet ETH 没有市场。有可能从[水龙头](https://faucet.rinkeby.io/)中得到一些 ETH。一些测试网是:

○Sepolia
○Ropsten
○Rinkeby
○Kovan

EVM 的
以太坊虚拟机(EVM)是一个计算引擎，它就像一台分散的计算机，拥有数百万个可执行的项目。它被认为是以太坊中运行执行和智能合约部署的部分。EVM 是一个软件框架，允许开发人员构建基于以太坊的分散式应用程序(DApps)。所有以太坊账户和智能合约都存储在这台虚拟电脑上。合同通常用 Solidity 这样的高级语言编写，然后编译成 EVM
字节码。这意味着机器代码与主机的网络、磁盘和其他操作是分离的。以太坊网络中的每个节点运行一个 EVM 实例，允许它们同意执行相同的指令集。

**EVM 是如何工作的？EVM 的目标是计算出区块链上每个街区以太坊的整体状态。以太坊，像其他区块链的网络一样，有自己的本地货币(ETH ),并采用分布式账本来跟踪交易，同时对用户如何与网络互动施加特殊的规则。另一方面，以太坊由于其智能合约功能而提供了额外的功能层。EVM 是以太坊协议和以太坊系统共识引擎的关键组件。它使任何人都可以在一个不可信的生态系统中运行代码，在这个生态系统中，执行的结果可以得到保证并且是完全可预测的(即，智能契约执行)。跟踪执行成本的系统向在 EVM 上实现的每个指令分配以 Gas 为单位的相关成本。**

**用什么语言写智能合同？可靠性是用来建立智能合同的最流行的语言。它是一种面向对象的高级语言，用于实现智能合约。Solidity 受 C++、Python 和 JavaScript 的影响很大，它是针对以太坊虚拟机(EVM)设计的。它是静态类型的，支持继承、库和复杂的用户定义类型以及其他特性。**

为了快速进行可靠性编程，可以使用一种流行的 IDE，称为 Remix IDE。在线 IDE 最适合于快速入门和尝试开始编写 Solidity 语言、在 EVM 上部署合同、兼容性和其他相关领域，但当涉及到智能合同的实际开发时，它可能不会非常方便。对于 solidity 代码的实际开发和编写，我一直使用带有 Solidity 扩展的 Vscode。

以太坊有不同的开发环境。一些受欢迎的是安全帽，布朗尼，Embark 和松露。我已经使用所有三种环境初始化了我的项目，但是选择了 HardHat 进行开发。这些环境使您能够轻松部署智能合约并测试智能合约。设置一个 HardHat 环境是一个非常简单直接的过程:

> 使用 Yarn 作为包管理器
> 安装:
> Yarn add–dev hard hat//将其作为开发依赖项添加
> 初始化
> yarn hardhat >创建简单项目

这将为我们提供一个基本的部署配置脚本、样本契约和所需的测试文件夹。从那里可以编写将被添加到可用任务列表中的任务。使用 hardhat 编写脚本要容易得多，开发时间也短得多。

**智能合约中的测试** 增强智能合约安全性的最关键步骤之一就是测试它们。在以太坊网络上发布合同之前进行彻底的测试是至关重要的，因为与传统软件不同，智能合同在启动后通常无法修改。该术语指的是在开发周期中仔细检查和评估智能合同以确定其源代码的
级别的过程。测试智能合约可以最大限度地降低软件故障的可能性，从而减少代价高昂的漏洞利用，并使发现缺陷和漏洞变得更加简单。Rekt 列出了一些已成为攻击、利用或套利受害者的 defi 协议的名称。给出的列表显示了测试的严肃性。

测试智能合约非常重要，因为代码将对所有人公开，如果没有实施适当的测试，任何人都可能穿透松散的端点。在 hardhat 中，可以使用普通的 javascript 测试库(如 Chai)来执行测试。然后，我们可以执行单元测试、集成测试和系统测试。对于某些场景，测试实现可能有点昂贵和困难，所以我们可以使用 mocking 来模拟实际行为，并确保我们的脚本按照它们应该的方式运行。

**实施**

DAPP 将有 3 个应用程序:

*   智能合同(后端)
*   管理面板(前端)
*   移动应用程序

# 后端(智能合约)

[后端](https://github.com/jedisam/Refund-by-location-smart-contract)是使用 solidity 开发的，这将使组织的管理员能够添加和管理员工，并在随机时间接收员工的坐标。测试脚本是使用 chai(一个 javascript 测试框架)编写的，以确保一切工作正常。

```
//SPDX-License-Identifier: Unlicensepragma solidity ^0.8.0;import “hardhat/console.sol”;contract Refund {address owner;struct EmployeeDetail {string name;uint256 lat;uint256 lon;uint256 allowedDistance;}struct conditionDetail {string status;bool condition;}mapping (address => EmployeeDetail) employeeDetail;// mapping(address => bool) public employeeCondition;mapping(address => conditionDetail) public employeeCondition;address[] public employees;constructor() {owner = msg.sender;console.log(“Deploying a Refund with Owner:”, owner);}function createEmployee(address empAddress, string memory name, uint256 lat, uint256 lon, uint256 allowedDistance) public {// restrict employee creation to ownerrequire(msg.sender == owner);// set User name using the employeeDetail mappingemployeeDetail[empAddress].name = name;// set Employee lat using the userStructs mappingemployeeDetail[empAddress].lat = lat;// set Employee lon using the employeeDetail mappingemployeeDetail[empAddress].lon = lon;// set the Employee’s allowed distance using the employeeDetail mappingemployeeDetail[empAddress].allowedDistance = allowedDistance;// Set default employee condition to not verifiedemployeeCondition[empAddress].status = “Not Verified”;employeeCondition[empAddress].condition = false;// push user address into userAddresses arrayemployees.push(empAddress);}function getEmployeeDetail(address empAddress) public view returns (string memory, uint256, uint256, uint256) {return (employeeDetail[empAddress].name, employeeDetail[empAddress].lat, employeeDetail[empAddress].lon, employeeDetail[empAddress].allowedDistance);}function ingestCoordinate(address empAdderss, uint256 lat, uint256 lon, uint256 etimestamp) public {require(etimestamp >= 0 && etimestamp <= 12 || etimestamp >= 18 && etimestamp <= 22);uint256 distance = calculateDistance(lat, lon);if (distance > employeeDetail[empAdderss].allowedDistance) {// employeeCondition[msg.sender] = false;// Set default employee condition to not verifiedemployeeCondition[empAdderss].status = “Verified”;employeeCondition[empAdderss].condition = false;} else {employeeCondition[empAdderss].status = “Verified”;employeeCondition[empAdderss].condition = true;}}function getEmployees() public view returns (address[] memory) {return employees;}function payEmployee(address empAddress, uint256 amount) public payable{require(msg.sender == owner);require(amount > 0);require(employeeCondition[empAddress].condition);// require(employeeCondition[empAddress].status == “Verified”);payable(empAddress).transfer(amount);}// Find the square root of a number using the Babylonian methodfunction sqrt(uint x) public pure returns (uint y) {uint z = (x + 1) / 2;y = x;while (z < y) {y = z;z = (x / z + z) / 2;}return y;}function calculateDistance(uint256 lat2, uint256 lon2) public view returns (uint256 dist){(,uint256 lat1, uint256 lon1,) = getEmployeeDetail(msg.sender);uint256 distance = uint256(sqrt((lat2 — lat1) ** 2 + (lon2 — lon1) ** 2));return uint256(distance);}}
```

最后，我使用 hardhat 将智能合约部署到 rinkeby testnet。我用 rinkeby 水龙头公司的免费乙醚在区块链进行交易。在部署智能合约时，将返回并记录一个地址，因为前端将使用该地址与后端进行通信(智能合约)。它还将在项目目录中生成一个应用程序二进制接口(abi ),这是一种与以太坊生态系统中的合同进行交互的标准方式，既可以来自区块链之外，也可以用于合同到合同的交互。

# 前端

前端到后端智能合约交互主要需要三样东西，它们是:

*   应用程序二进制接口
*   智能合同地址
*   客户端库(ethers…)

前两个在上面的智能合约部署中生成。对于客户端库，可以使用 ethers 或任何其他库来调用智能契约。

[管理面板](https://github.com/jedisam/refund-smart-conract-mobile-app)使用 react(typescript)构建，这将使管理员能够通过与区块链交互来管理员工。

[手机应用](https://github.com/jedisam/refund-smart-conract-mobile-app)是使用 Expo(react-native 的一个简化版本)开发的，它将跟踪员工并将他们的坐标和时间戳一起发送到区块链。然后，区块链将检查是否在指定的时间范围内，然后根据员工给定的距离计算他们的距离。如果他们的位置不符合协议，它会显示给管理员。

为了能够在后台发送员工的坐标，我使用了一个名为 [background-fetch](https://docs.expo.dev/versions/latest/sdk/background-fetch) 的包，它将能够在随机时间被触发时在移动应用程序的后台运行。

# 结论

可以看出，通过使用智能合同，人们可以提供可靠且透明的系统，这将使管理系统更加容易。

**未来作品。在未来，我将致力于将支付整合到系统中，这样员工就可以用乙醚支付了。**

管理应用:[https://refund-by-location.netlify.app/](https://refund-by-location.netlify.app/)

GitHub:[https://GitHub . com/jedisam/Refund-by-location-smart-contract](https://github.com/jedisam/Refund-by-location-smart-contract)