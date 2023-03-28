# 周二早上的威胁报告:2022 年 12 月 13 日

> 原文：<https://medium.com/codex/tuesday-morning-threat-report-dec-13-2022-a1ae194f6a40?source=collection_archive---------12----------------------->

消息总是坏的，但分析总是好的。

![](img/5b469b52f056cf2239eb96615c38d8bc.png)

马库斯·斯皮斯克拍摄的图片

大家早上好，星期二快乐！

一家法国医院遭到网络攻击，黑客们互相追逐，为了好玩，台湾+德克萨斯对战抖音。让我们开始吧！

# 头条新闻:

*过去 7 天最大头条。下面的分析部分。*

[**法国医院遭网络攻击**](https://securityaffairs.co/wordpress/139316/cyber-crime/french-hospital-ransomware-attack-2.html) :凡尔赛医院中心因网络攻击被迫转移病人并取消部分手术。

[**新 WAF 绕过攻击**](https://securityaffairs.co/wordpress/139445/hacking/web-application-firewalls-waf-bypass.html):Web 应用防火墙(WAF)通过过滤和阻止传入流量来帮助保护服务器。许多企业依靠 WAFs 来保证安全。研究人员发现了一种能够绕过几种不同类型 waf 的技术。这使得研究人员可以绕过保护，直接与服务器进行交互。

[**狗咬狗的世界**](https://www.theregister.com/2022/12/08/scammers_scam_cybercriminals_sophos/) :仅仅在三个论坛上，骗子们在过去的 12 个月里就从对方那里偷走了 250 万美元**。**

**[**全球间谍软件爆炸**](https://dnyuz.com/2022/12/08/how-the-global-spyware-industry-spiraled-out-of-control/) **:** 世界各地的政府都在使用间谍软件监视他们的对手和他们自己的公民。这篇文章着眼于蓬勃发展的间谍软件销售行业。**

**[**SiriusXM 使汽车易受攻击**](https://thehackernews.com/2022/12/siriusxm-vulnerability-lets-hackers.html) :在一个新发现的影响某些本田、讴歌、英菲尼迪、尼桑的漏洞中，一个 SiriusXM 漏洞可以用来解锁和启动汽车。要执行黑客攻击，只需要汽车的 VIN(车辆识别号)。**

**[**中国 APT 黑中东电信公司**](https://thehackernews.com/2022/12/chinese-hackers-target-middle-east.html) :中国高级持续威胁(APT)集团后门外交，黑了一家中东电信公司。**

**[**俄罗斯黑客瞄准美国军事供应商**](https://thehackernews.com/2022/12/russian-hackers-spotted-targeting-us.html) :美国军方的武器和硬件供应商环球军械(Global armour)被一个与国家有关联的俄罗斯黑客组织盯上。**

**[**美国运输安全管理局面部识别**](https://www.theregister.com/2022/12/06/us_transportation_security_agency_facial/) :美国运输安全管理局(TSA)正在研究为乘客使用面部识别技术。目前已在 16 个机场实施，美国运输安全管理局正考虑明年在全国推广。**

# **我的外卖**

***根据本周的新闻和我的行业经验分析。更多标题在下面较低的梯队。***

**F 20 年前，“城堡-护城河”思维是一种流行的网络安全思维。在“城堡-护城河”中，你认为公司资源是城堡，虚拟专用网络(VPN)是保护城堡的护城河。这种心态依赖于 VPN 是一种强大的保护，因为 VPN 是保护公司资源免受互联网攻击的工具。然而，如果 VPN 是一个薄弱的保护，恶意行为者能够绕过 VPN。这为访问数据和破坏公司系统提供了更直接的途径。**

**本周的新闻显示，研究人员发现了一种绕过多种 VPN 的新方法。入侵 VPN 是不同中国政府黑客组织最喜欢的技术。当黑客拥有可以入侵 VPN 的零日漏洞时，让城堡的墙“更高更厚”的古老格言就失效了。通常，这些黑客的工作方式是首先绕过 VPN 进入公司网络。一旦上线，观察流量并从一个业务部门的服务器“横向移动”到另一个业务部门的服务器就容易多了。**

**随着 VPN 变得越来越不可靠，两个安全概念成为了人们关注的焦点:**

1.  **“数字身份”**
2.  **“零信任”**

**“数字身份”确保公司知道网络上的每一个连接，而不是信任 VPN 上的每一个人。数字身份回答了这个问题，“谁是那个连接？”完善的数字身份是“零信任”的必要前提“零信任”的关键概念是质疑一切。在允许使用资源之前，应该对访问的每一项公司资源进行询问和验证。因此，即使 VPN 遭到黑客攻击，强数字身份与零信任的结合也能减轻损害。VPN 黑客可以让黑客进入网络，但是一旦进入网络，数字身份就会问:“你是谁？”。零信任策略会问，“你为什么需要访问这个？”一旦不良行为者无法证明他们是具有真实业务需求的合法员工，他们与公司网络的连接就会被切断。**

# **下层梯队:**

***有趣的网络安全新闻没有成为头条新闻。***

**[**台湾禁用抖音**](https://www.theregister.com/2022/12/07/taiwan_bans_chinese_platform_tiktok/) :台湾出于安全考虑，在政府设备上禁用抖音。最近，更多的联邦和州政府制定了抖音禁令，包括德克萨斯。**

**[**俄罗斯第二大银行 DDoS'd**](https://heimdalsecurity.com/blog/russian-bank-vtb-is-currently-under-the-largest-cyber-attack-in-its-history/amp/) :俄罗斯第二大银行遭遇史上最大网络攻击。该攻击是分布式拒绝服务(DDoS)攻击。**

**[**Internet Explorer Zero Day**](https://gbhackers.com/internet-explorer-zero-day-vulnerability/)**:**微软遗留浏览器 Internet Explorer 中发现一个“Zero Day”(新)漏洞。谷歌的威胁小组报告称，朝鲜黑客组织正利用谷歌攻击韩国受害者。**

**[**1B 零售商记录曝光**](https://www.hackread.com/online-retailer-exposed-user-data/):2022 年 4 月至 7 月期间，在线零售商 Vevor 超过 10 亿条用户记录被曝光。暴露是错误配置的结果。**

**[**雨刷冲击钻石行业**](https://heimdalsecurity.com/blog/diamond-industry-attacked-by-iranian-hackers-with-data-wiping-malware/) :雨刷是寻求删除数据、损害公司的恶意程序。一个伊朗黑客组织以香港、以色列和南非的钻石公司为目标。**

**[**无人机安全**](https://securityaffairs.co/wordpress/139196/hacking/drones-abuse.html) **:** 无人机预计到 2030 年将是一个 900 亿美元的产业。这篇思想文章着眼于无人机将对网络安全带来的所有挑战。**

**[**Android 木马**](https://thehackernews.com/2022/12/researchers-uncover-darknet-service.html) :安全研究人员发现了一种恶意软件服务，它让黑客在 Android 应用商店的合法应用程序中添加恶意代码。**

**[**如何提高网络安全弹性**](https://www.helpnetsecurity.com/2022/12/07/factors-cybersecurity-resilience/) :这篇文章着眼于提高网络安全弹性的 7 大方法。**

**[**SBOM 推**](https://www.securityweek.com/big-tech-vendors-object-us-gov-sbom-mandate):SBOM 的软件物料清单可以被认为是科技的“营养标签”。它是一个软件产品中使用的所有开源代码的列表。大型科技公司正在抵制有关 SBOMs 的新法规。**

**在这个页面的右边，你可以**关注**和**订阅**每周在你的收件箱里收到这个时事通讯(不需要中型账户，只需要用 Google 登录)！**

**感谢阅读！下周见！**

*****关于作者*** *: Mark 是一名网络安全架构师，也是领先的网络安全咨询公司*[*Aujas*](https://www.aujas.com/)*的顾问。***

**![](img/3402174710e0b44a759134dc4c37f528.png)**