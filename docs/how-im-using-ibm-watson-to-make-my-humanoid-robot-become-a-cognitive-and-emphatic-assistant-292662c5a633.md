# 我如何使用 IBM Watson 让我的人形机器人成为一个认知和强调的助手。

> 原文：<https://medium.com/codex/how-im-using-ibm-watson-to-make-my-humanoid-robot-become-a-cognitive-and-emphatic-assistant-292662c5a633?source=collection_archive---------2----------------------->

## [法典](https://medium.com/codex)

![](img/e07948b03397905cafb006c072045a4c.png)

今晚，受到第二次看《终结者 2》的启发(我喜欢那部电影和它的原声)，我终于完成了我的[认知箱](https://www.linkedin.com/pulse/building-my-cognitive-box-ibm-watson-raspberry-pi-jair-ribeiro)项目的第二阶段……嗯..这篇文章会有点长…

我每天下班后都在做它，直到很晚……(我女朋友真有耐心)把所有的东西放在一起:硬件(树莓工作非常流畅)和软件(主要是 IBM Watson 平台上的云软件)和一些脚本……

我最初的想法是使用人工智能或者我更喜欢说的**认知计算**来构建一个能够倾听、理解和回答各种问题的虚拟助手，但每天我都在努力，我的期望越来越高，直到今天我一直在给这个虚拟助手添加一些功能和要求..当我想到我已经到达了第一个里程碑…但是路还很长！！

# 以沃森的名义

首先，我开始思考我的生物的名字。因为 Jarvis 已经被取了，所以我决定取一个神秘的字母数字的名字，但是对我来说意义重大；于是我把它叫做 TJ-800:基本上是以来自 IBM 的 [T.J. Watson](https://en.wikipedia.org/wiki/Thomas_J._Watson) 和 [T-800](https://en.wikipedia.org/wiki/Terminator_(character)#Judgment_Day) (阿诺德在[终结者 2 —审判日](https://en.wikipedia.org/wiki/Terminator_(character)#Judgment_Day)中演绎的好电子人)命名的。

# 为我的机器人塑造个性

第二件事，我需要为我的助手定义一种个性。我一直在与 IBM Watson 一起开发聊天机器人，基本上，我的虚拟助手的心脏和大脑将是一个多用途的聊天机器人。但是我想清楚地定义我的机器人的个性...我希望它以某种方式回答问题，用某种语气，尽可能多的带着幽默感和同情心。

我开发的所有聊天机器人都使用 [Watson Tone Analyzer](https://www.ibm.com/watson/services/tone-analyzer/) 来理解他们正在进行的对话的语气，并且 **TJ-800** 当然将能够实时分析它，并在生气、高兴或其他情绪的情况下做出反应。

基于情感的反应意味着**同理心**和**同理心**是现代聊天机器人的发展目标，我想在接下来的几个月里和 **TJ-800** 一起研究它。

关于个性，我决定 TJ-800 会用我能回答的非常相似的方式来回答我的问题。它将尽可能与我的幽默感和讽刺感相似……为了创建我的**社交人格**的数字副本，我决定获取数据来建立我的认知基线，当然，挖掘我的**社交媒体**个人资料。

有什么比社交媒体更好的数据集来代表我生活的数字副本呢？社交媒体储存了我过去几年的记忆和想法。所以我从**脸书**和 **Whatsapp** 开始。

# 收集我的社交数据

脸书和 Whatsapp 让我可以选择下载过去几年的全部信息。我只需要将它们转换成 Watson 可用的格式(意图、实体和对话)。这是一项正在进行的工作..而且我会花很多时间来完成这个任务。

Whatsapp 的信息下载更具体一些:我可以选择任意一个联系人的档案作为对话的来源，这个决定很简单:我选择我女朋友的信息有两个原因:它们大多是英文的(当然也有一些波兰语),数据集与一整套表情和场景最相关。当然，不会暴露任何个人数据…以防有一天我决定为其他人开放这个聊天…但我只是希望我的聊天机器人不会变得太浪漫:-)

事实上，我已经有了类似于 7.536 的答案——来自一整套 13.000 个 excel 行的问题，将被用作我和我女朋友对话的基线对话，很快我将开始把它加载到聊天机器人的认知引擎中。一个有 7536 个回答的数据集不足以提供一个刺激的、动态的我的社交个性副本，但这是一个很好的起点。

脸书的档案很大……你可以想象……我在脸书上非常活跃，我有超过 **120.000** 个 excel 行需要解析和清理，然后才能用在我的聊天机器人上。

为了管理我来自脸书的**社交人格**数据集，我创建了一个 excel 文件，现在我将它按照语言划分为[(在过去的几年里，我一直在用葡萄牙语、英语、意大利语和波兰语聊天)，将问答格式的所有信息配对，并删除重复的信息。这将是一个漫长的工作！！](http://www.mousewhisperer.co.uk/drivebunny/detect-language-in-google-sheets/)

# 给 TJ-800 一具尸体

现在, **TJ-800** 的大脑和心脏被定义了，是时候考虑它应该是什么样子了。有数以千计的选项、几种类型的机器人、DIY 工具包和其他东西，经过深入研究，我决定选择 WowWee 的[机器人](http://wowwee.com/robosapien-x)。

这个神奇的机器人具有先进的生物形态特征和非常动态和流畅的运动和手势，67 个预编程功能，完全可编程并通过红外命令。我所要做的就是识别如何使用内置的[红外加密狗](https://github.com/WowWeeLabs/RoboRemote-IR-Dongle-SDK)在聊天机器人对话期间向机器人发送正确的信号。

基本上，机器人通过它的红外遥控器进行交流，它有一个加密狗来使用它的应用程序连接到我的智能手机。我发现有一种方法可以通过播放 WAV 文件来控制机器人。我在 [WowWeeLabs GitHub](https://github.com/WowWeeLabs/RoboRemote-IR-Dongle-SDK) 页面上看到的，于是我下载了 WAV 文件，开始控制机器人将 IR Dongle 连接到我的 [CognitiveBox](https://www.linkedin.com/pulse/building-my-cognitive-box-ibm-watson-raspberry-pi-jair-ribeiro) 音频插孔！找到了。！！！！

# 经过技术面！！

从技术上来说，我的 TJ-800 是沃森服务的组合，如[对话](https://www.ibm.com/watson/services/conversation/)、[语音转文本](https://www.ibm.com/watson/services/speech-to-text/)、[语气分析器](https://www.ibm.com/watson/services/tone-analyzer/)，以及[文本转语音](https://www.ibm.com/watson/services/text-to-speech/)、[沃森物联网](https://www.ibm.com/internet-of-things/)，以及一些其他服务。js 脚本；所有东西都由[节点红色](https://nodered.org/)连接。为了把所有东西放在一起，我研究了在 GitHub 和 Medium 上找到的一系列好文章，并把它们改编到这个项目中。

最关键的部分，我仍在努力改进它…是控制机器人的动作并使其与对话同步，但注入正确的音频文件以在任何沃森文本到语音转换回答后播放是使 **TJ-800** 身体与其答案同步的最简单方法。你可以在这个视频中看到第一个结果:

# 在发展的第一阶段吸取的经验教训

1.红外传感器必须指向机器人，这稍微降低了该解决方案的实用性..到目前为止，还没有其他方法来控制机器人..(这个机器人的蓝牙版本[很贵，我不想花那么多钱)。](http://wowwee.com/robosapien-blue)

2.有时，沃森服务需要很长时间来回答树莓( [CognitiveBox](https://www.linkedin.com/pulse/building-my-cognitive-box-ibm-watson-raspberry-pi-jair-ribeiro) )的问题，它可能会超时。也许这是一个延迟问题，也许这取决于树莓…我会找到这个问题的答案。

正如菲尔·吉尔伯特所说:[一切都是原型](https://www.youtube.com/watch?v=8OQLpO_IJzw)，它仍然是一项正在进行的工作。还有很多可以改进的地方，但是我对第一个结果非常满意。真的很有前途。

我很乐意尝试用认知计算来挑战自己。如果你喜欢它，并想检查我在这个项目的下一步…跟着我，我们会保持联系。

谢谢

贾伊尔·里贝罗

![](img/e34778fd6c5518f469aad0973be772bd.png)

负责数据中心迁移的 Wave 项目经理|设计思考者| IBM 的认知学习领导者

*原载于 2017 年 9 月 8 日*[*【https://www.linkedin.com】*](https://www.linkedin.com/pulse/how-i-used-watson-make-my-humanoid-robot-become-empathic-jair-ribeiro/?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_view_base_post_details%3BoVcRXp9dS%2BOl0nfB2CzWYw%3D%3D)*。*