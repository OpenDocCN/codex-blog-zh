# 如何追踪贵金属的价格

> 原文：<https://medium.com/codex/how-to-track-the-prices-of-precious-metals-9a69126284cb?source=collection_archive---------19----------------------->

![](img/4d7ca1e43fa8ad2d27e3c91947177ac2.png)

作者图片

现在是 2022 年初，如果你和我一样，你一定在想如何让你手中的货币单位保值。为什么你会有这样的想法？答案很简单。每个货币单位的价值决定了你可以用这个单位交换多少能量或食物。如果你还没有注意到，你可能会花费越来越多的货币单位来购买 x 数量的能量或 *y 数量的食物*，而不是你之前花在相同数量的 *x* 和 *y* 上的钱。

根据美国劳工统计局(U.S. Bureau of Labor Statistics)的数据，消费者价格指数(CPI)是“衡量城市消费者为一篮子消费品和服务支付的价格随时间平均变化的指标”，而“CPI 衡量的是消费者在日常生活支出中经历的通货膨胀”。

我会再写一篇关于如何观察 CPI 值的帖子。然而，在这篇文章中，我想重点谈谈如何保护自己免受通货膨胀的影响。有不同的方法来解决这个问题，但是正如这个博客的标题所表明的，我将集中讨论其中的一种方法。

根据[韦氏词典](https://www.merriam-webster.com/dictionary/precious%20metal),***贵金属*** 的定义是“任何不太常见且高价值的金属(如金、银、铂金属)”。将一定数量和类型的贵金属兑换成一些货币单位的行为必须根据贵金属的价值来确定时间。一个人如何知道所说的贵金属的价值？我的搜索把我带到了 LMBA。

我想自动跟踪贵金属的价格，这样我就可以立即知道什么时候该采取行动。下面是我对跟踪四种金属价格的方法的实现:金、银、铂和钯。

# 密码

可以参考下面的嵌入式笔记本。

你也可以在这里访问 GitHub 要点[。以下是我大致遵循的步骤。](https://gist.github.com/troymyname/fe25dd2ea042a3ca7449601def94e7db)

1.  准备 URL 地址
2.  获取响应数据，并将数据写入本地文件
3.  读入一个*熊猫*数据帧，并清理索引和列名
4.  收集到单独的数据容器中
5.  计算数据的简单移动平均(SMA)和指数加权移动平均(EWMA ),并将它们添加到各个容器中
6.  配置 *matplotlib* 图，并从每个容器中绘制数据

# 结论

我想说清楚，我没有提供任何财务指导或建议。我只是在探索各种选择，并在此过程中实现自动化。

在学习了 GitHub gist 之后，你应该也能够自己追踪贵金属价格了。

感谢您今天花时间阅读这篇文章。请留下关于我如何提高我提供的内容质量的评论。如果你想支持我，让我产生更多这样的内容，你可以[给我买杯咖啡](https://www.buymeacoffee.com/tonmoyroy)。干杯，继续探索！

# 参考

[1]美国劳工统计局。(未注明)。*常见问题*。【https://www.bls.gov/bls/faqs.htm 

[2]韦氏词典。(未注明)。*贵金属*。[https://www.merriam-webster.com/dictionary/precious%20metal](https://www.merriam-webster.com/dictionary/precious%20metal)

[3] LMBA。(未注明)。 *LBMA 贵金属价格*。[https://www . lbma . org . uk/prices-and-data/贵金属价格#/](https://www.lbma.org.uk/prices-and-data/precious-metal-prices#/)