# 用 Python 三步实现 WhatsApp 消息自动化

> 原文：<https://medium.com/codex/automate-whatsapp-messages-with-python-in-3-steps-e3571fb0aa51?source=collection_archive---------0----------------------->

## 使用此 Python 脚本向联系人/群组发送 WhatsApp 消息

![](img/6b46b446aedda08678fe1e159f89eee6.png)

演职员表— [链接](https://images.unsplash.com/photo-1633354931133-27ac1ee5d853?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxzZWFyY2h8N3x8d2hhdHNhcHB8ZW58MHx8MHx8&auto=format&fit=crop&w=1600&q=60)

Python 可能是最酷的语言之一。它的迷人之处在于你可以自动化日常任务。

我们每天都在做的任务之一就是在 WhatsApp 上发送消息。

虽然这个任务就像拿起电话、打字、点击“发送”按钮一样简单，但我们有时会忘记在生日、纪念日等特殊场合发送祝愿信息。

所以你一定想知道我们能安排一个 WhatsApp 消息吗？

你肯定可以通过使用一个叫做 pywhatkit 的库。我们可以安排将消息发送给我们的任何联系人或某个群组。

以下是使用 Python 向联系人和群组发送 WhatsApp 消息需要遵循的 3 个步骤。

*免责声明:本文仅用于教育目的。在学习 Python 时，您可以使用这个库来安排生日/周年纪念消息。我不建议你使用它，以防你需要发送一条需要保持私密的消息。*

# 使用 pywhatkit 库向 WhatsApp 发送消息

Pywhatkit 是一个用于不同目的的 Python 库，其中之一就是发送 WhatsApp 消息。

这是一个易于使用的库，不需要任何额外的设置。也就是说，它有很多依赖项，所以我强烈建议您将它安装在一个新的虚拟环境中(在这里了解如何设置虚拟环境)。

# 步骤 1:安装 pywhatkit

打开终端并运行以下命令。

```
pip install pywhatkit
```

您可以使用命令`pip list`检查这个库安装的所有依赖项。

# 第二步:在 WhatsApp 上给某人发送消息

通过 WhatsApp Web 登录您的 WhatsApp 账户。

要使用 Python 和 pywhatkit 向 WhatsApp 联系人发送消息，我们使用下面代码所示的`.sendwhatmsg`方法(改为插入您的联系号码)。

```
**import** pywhatkit
*# syntax: phone number with country code, message, hour and minutes*
pywhatkit.sendwhatmsg('+91xxxxxxxx', 'Your Message', 12, 01)
```

这个代码意味着它将在“12:01”发送“你的消息”给联系人 X。现在运行这段代码，你会看到这样一条消息—

> 10 秒后，web.WhatsApp.com 将打开，15 秒后，Whatsapp 将发送一条消息。

太好了！！您已经使用 python 成功发送了一条消息。

# 第三步:向 WhatsApp 上的一个群发送消息

我们还可以向 WhatsApp 上的特定群发送消息，但首先，我们必须获得群链接。

一旦你有了组的链接，我们就必须使用`.sendwhatmsg_to_group`方法。这个方法类似于我们在步骤 2 中使用的方法，但是现在我们插入一个组链接。

```
**import** pywhatkit
*#syntax: group link, message, hour and minutes*
pywhatkit.sendwhatmsg_to_group("group-link", "Your Message", 12, 01)
```

就是这样！

在本文中，你已经成功地学习了如何使用 python 库" *pywhatkit "发送自动消息。*

# 最后的想法

我希望这篇文章对你有所帮助，并且是值得的。请随意将这篇文章分享给你的程序员朋友。

**快乐编码！**