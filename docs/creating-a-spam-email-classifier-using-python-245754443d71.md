# 使用 Python 创建垃圾邮件分类器

> 原文：<https://medium.com/codex/creating-a-spam-email-classifier-using-python-245754443d71?source=collection_archive---------6----------------------->

厌倦了收到不想要的邮件？通过训练一个 ML 模型来将电子邮件分类为垃圾邮件或 Ham，您可以干净地过滤每天收到的大量电子邮件。对于这项任务，我将尝试建立一个分类器，检测电子邮件是垃圾邮件或火腿(电子邮件打算阅读)。为此，我们将使用来自 https://spamassassin.apache.org/old/publiccorpus/的数据。使用文件 easy_ham & spam 我们将解析两个文件夹中每封邮件的主题&正文。让我们跳转到一些代码&开始数据预处理&清理步骤。