# 提升您的 Android 应用程序性能

> 原文：<https://medium.com/codex/boost-up-your-android-apps-performance-3c03d6fe266e?source=collection_archive---------6----------------------->

## 让你的应用在低端设备上表现出色的 11 个步骤。

![](img/616a93d68e8a910803aeec617cf68ee8.png)

照片由来自 [Pexels](https://www.pexels.com/photo/selective-focus-photography-of-gray-and-blue-moth-perched-on-brown-surface-1212755/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [Jonny Lew](https://www.pexels.com/@jonnylew?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 拍摄

我喜欢 Android 设备的原因是它对每个人来说都是负担得起的。
作为一名 Android 开发者，当你开始你的 Android 项目时，你必须考虑所有的硬件类型。

在撰写本文时，我们有各种各样的硬件，从最低的联发科到最高的 SnapDragon，从 ram 的半千兆到 ram 的 12+千兆。

假设你是一名成功的 Android 开发人员，买得起非常高端的设备，这对你的工作和生活都有帮助，但是…
想想你的用户，他们能买得起你的同类型设备吗？答案是否定的。

我能听到有人说“我在这个领域工作了很多年，没有人抱怨过我的表现，我为什么要烦恼呢？”

# 我为什么要烦恼？

*   **以示尊重:**
    你的所有用户都值得拥有同样卓越的用户体验。
*   **商机:**
    你准备好冒失去一大块市场的风险了吗？大公司、大餐馆、多地点杂货店等等，倾向于使用低端设备来削减开支，你最好考虑这些机会，以利用其他应用程序和开发者的优势。

# 在我们走之前:

我们之前讨论过清洁架构和 MVI，以及如何设置您的项目来使用它们，我不会重新发明轮子，所以我将从我们停止的地方开始。
你可以在这里查看之前的文章:

[](/@mohammed.saif.prog/your-first-clean-mvi-android-project-a93cfe00080e) [## 你的第一个干净的 MVI 机器人项目

### 我最喜欢的设置所有项目的方式。

medium.com](/@mohammed.saif.prog/your-first-clean-mvi-android-project-a93cfe00080e) 

提到的文章是为了让你的生活更轻松，但是用户的生活呢！

# 我可以采取哪些措施来提高我的应用程序的性能？

# 1-更聪明地思考

用聪明的方法来减少做同样工作的流程数量。

*   如果你的应用程序背后有一个服务器，尽量把沉重的负载放在服务器上，不要在本地处理你所有的数据和报告。
*   如果您从本地数据库获取数据，请使用现成的数据库操作来优化您的查询，以使数据随时可用。

# 2-使用线程

*   利用线程和并行作业，如 react VX(rx Java)和协程。
*   在 ReactivX 中，你可以很容易地在一个线程上订阅，在另一个线程上观察，不要忘记在 onDestroy/onCleared 中处理你的所有订阅者。协程也有一些很酷的东西来专业地使用线程，你可以在 IO 线程中启动进程，然后在主线程中完成它。

# 3-对您的数据进行子建模

当从 API 或数据库请求数据时，只问你需要什么，不要害怕制作和使用子模型。
我的意思是，如果你有一个有 50 个字段的表，而你需要显示的只是像 4-5 这样的几个字段，为什么要让数据流过载并填满 ram，只需制作一个主模型的子模型并获得现成的数据，数据库也是如此，只需选择你需要的。

# 4-不要留下任何开放的连接

这是一个坏主意，这样做，它可能会导致异常和溢出内存，你甚至可以得到一个死锁和冻结你的应用程序，关闭任何打开的连接，一旦你做了。

# 简化你的用户界面

在 UI 层(XML)尽量简化 UI，越深入越慢。
通过减少用户界面的深度来帮助你的应用程序更快地渲染，约束布局在这方面是惊人的，指南可以帮你很多。

# 6-绑定您的视图和数据

使用数据绑定和视图绑定，这将帮助您直接将数据注入到 UI 中，不需要所有 findViewById 的东西，摆脱它。

# 7-分页

在适配器中使用分页，一次请求所有数据是非常糟糕的做法，如果数据库中有 100k 行，低端设备能处理吗？甚至不是高端的。

# 8-优化数据库查询

剖析和调试您的数据库查询，这可能会有很大的不同。
如果你的查询有点复杂，你可能想再看一眼，帮助数据库更快地获得你的数据。

# 9-使用本地库

尽可能远离第三方库，一些库的实现很差，即使它给了你想要的，它也可能是你应用程序中的瓶颈。

# 10-抽屉和布局

按屏幕大小划分你的 drawables 和布局，不要担心应用程序的大小，aab 会处理的。
Android 足够聪明，可以选择与屏幕尺寸非常匹配的可拉伸/布局，5 英寸的低端设备与 65 英寸的屏幕不一样。

# 11-现实点

在真正的低端设备上测试你的 app，或者使用可定制的 Android 模拟器，内置的 Android 模拟器太好了，用不上。

# 结论…

每一个用户都很重要，无论您正在开发什么类型的应用程序，性能都可以让您的应用程序从零到英雄，不要把钱留在桌子上，并优化您的应用程序以适应所有类型的硬件。

# 参考资料…

[](https://carto.com/help/tutorials/common-sql-operations/) [## 常见 SQL 操作

### 教程/使用 SQL /基本 SQL 在制作地图时，向您介绍一点我们…

carto.com](https://carto.com/help/tutorials/common-sql-operations/) 

[https://developer . Android . com/topic/libraries/architecture/paging/v3-概述](https://developer.android.com/topic/libraries/architecture/paging/v3-overview)

[https://developer.android.com/topic/libraries/data-binding](https://developer.android.com/topic/libraries/data-binding)

[https://developer . Android . com/training/multi screen/screen densities](https://developer.android.com/training/multiscreen/screendensities)