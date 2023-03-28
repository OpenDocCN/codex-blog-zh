# 如何在不弄乱 Git 历史的情况下引入代码格式化程序

> 原文：<https://medium.com/codex/how-to-introduce-a-code-formatter-without-messing-up-git-history-4a16bd074c10?source=collection_archive---------1----------------------->

## 我们仍然想知道格式化后是谁以及为什么修改了一行代码

![](img/991f2260b41a00ca7594330cd5908d53.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由[法扎德·纳齐菲](https://unsplash.com/@euwars?utm_source=medium&utm_medium=referral)拍摄的照片

在所有最近的 JavaScript 项目中，我们都使用了[漂亮的](https://prettier.io)作为自以为是的代码格式化程序。Prettier 实际上是固执己见的，因为它只支持少数定制选项。这是文件中的一段引文…