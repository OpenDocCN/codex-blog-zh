# 孤立的 AWS S3 触发器

> 原文：<https://medium.com/codex/orphaned-aws-s3-triggers-3bff75f49e10?source=collection_archive---------15----------------------->

## 阻止您触发新的 AWS Lambda 函数

![](img/99be858c461bb201b181ffc79bb0c063.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Max Duzij](https://unsplash.com/es/@max_duz?utm_source=medium&utm_medium=referral) 拍照

你遇到过这种情况吗？你在 AWS Lambda 中创建了一个新函数，你想从 AWS S3 中触发它，但它不允许，因为已经存在其他一些冲突的触发器，具有重叠的配置？但是另一个触发器存在于您已经删除的功能中？

我有。

昨天开始的。我想也许我可以等一天，给 AWS 一些时间来清理挥之不去的孤立配置。

没有骰子。

所以我去寻找冲突重叠的触发点。当然不在我删除的 Lambda 函数中。所以，也许在 S3 桶里会触发这个功能？

在 AWS DynamoDB 中，表有一个非常有用和方便的“Triggers”选项卡，它准确地显示了表上存在哪些触发器。S3 有类似的东西吗？

不，不是“触发器”

相反，它被命名为“事件通知”，可以在 S3 存储桶的 Properties 选项卡上找到，位于 CloudTrail 配置之下，EventBridge 配置之上。

它会告诉你每个触发器的标准和目的地。瞧，在我的例子中，它显示了删除的 AWS Lambda 函数。

因此，我们删除它，回到添加触发器到我们的新功能，然后嘣！接受。

从一个工具到下一个工具，一致性是非常重要的。显然，很难保持各种工具的一致性。也许有些人正在更新新的格式，而其他人正在等待更新。也许没有人意识到一个工具是不一致的。

如果您发现这样的 UI 问题，请帮自己一个忙，告诉您的提供商。在这种情况下，AWS:在屏幕底部有一个反馈按钮。使用它。告诉他们。他们会听的。

# 关于作者

Veltstra 是一个脾气暴躁的老软件架构师和开发人员，他在过去的 25 年里一直对糟糕的工具愤怒不已，并就如何提高可用性和可访问性提供建议。随意伸手！