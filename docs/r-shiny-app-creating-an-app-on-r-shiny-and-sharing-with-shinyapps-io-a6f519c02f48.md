# R Shiny App——在 R Shiny 上创建应用，并与 Shinyapps.io 共享

> 原文：<https://medium.com/codex/r-shiny-app-creating-an-app-on-r-shiny-and-sharing-with-shinyapps-io-a6f519c02f48?source=collection_archive---------1----------------------->

![](img/32d647bf43d3c798e96796e1cb960148.png)

设计生态学家在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

R 提供了一些非常直观的图表，所有使用过 R 的人也都非常熟悉 R shiny。R shiny 使我们能够创建交互式仪表板，从而充分利用 R 绘图功能以及反应式图形和表格。这对于希望拥有一个类似 BI 工具的环境以及 R 包的统计计算能力的分析师和研究人员来说尤其有用。

在这篇博客中，我展示了我们如何使用 R Shiny 创建交互式 dashboard，以及如何在 Shinyapp.io 上共享您的应用程序。我使用了 Data Flair 的免费数据集([https://Data-Flair . training/blogs/Data-science-project-ideas/](https://data-flair.training/blogs/data-science-project-ideas/))。这些数据用于使用 K-means 聚类算法进行客户细分。我在这里没有太注意模型的准确性，因为这个博客的唯一目的是展示 R 闪亮的应用程序。我在仪表板中创建了菜单和子菜单，它们清楚地突出显示了聚集这些数据所涉及的步骤。

因为 R Shiny 提供了使您的仪表板交互的能力，所以我让用户通过滑块输入来选择集群编号。集群的数量会相应地改变图形，用户还可以查看集群统计信息的变化。我使用了邓恩指数作为衡量标准来评估算法。

尽管聚类的数量可以由一些统计技术(这也是我的解决方案的一部分)和领域知识来决定，但是我认为让用户在不担心代码的情况下玩一玩图表会很有趣。

**你可以在这里查看完整的仪表盘:**[https://ruchideshpande.shinyapps.io/csapp/](https://ruchideshpande.shinyapps.io/csapp/)

我的代码将 UI 和服务器代码放在一个名为 app.R 的 R 文件中，库和数据也可以加载到同一个文件中。这里，除了用于集群的常规库之外，还加载了库 shiny 和库 shinydashboard，这两个库对于 R shiny dashboard 是必需的。

UI 部分展示了您的仪表板的外观。这里定义了仪表板主体以及标题、菜单和子菜单。

代码的服务器部分有点复杂，主要是因为所有的输出表、图表都嵌套在 shinyServer 函数中，以使输出具有反应性。我的建议是小心使用括号，并寻找合适的渲染函数，因为在渲染函数中编写代码类似于编写任何 R 代码。

一旦你熟悉了 shiny 函数，创建 R shiny 仪表板就很容易了，因为构建任何模型的代码与用 R 编写代码是一样的。

我故意避免分享创建应用程序的步骤，因为 https://shiny.rstudio.com/有非常详细的文档和视频给初学者。如果你们中的任何一个阅读我的博客的人有任何问题，我很乐意帮忙。

请在这里分享您的意见、建议和反馈！！