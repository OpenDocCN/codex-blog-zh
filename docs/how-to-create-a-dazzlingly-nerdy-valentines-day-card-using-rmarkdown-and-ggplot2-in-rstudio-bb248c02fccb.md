# 如何在 RStudio 中使用 RMarkdown 和 ggplot2 创建一张令人眼花缭乱的书呆子式情人节卡片

> 原文：<https://medium.com/codex/how-to-create-a-dazzlingly-nerdy-valentines-day-card-using-rmarkdown-and-ggplot2-in-rstudio-bb248c02fccb?source=collection_archive---------3----------------------->

## [法典](http://medium.com/codex)

![](img/6e09895cbe1f575b232b400bca8c59b3.png)

*使用 geom_line()参数创建心脏*

我做了一张情人节贺卡！在你点击/轻拍之前，还有更多。

# 背景

手工制作一张卡片或者用文字处理器和一些图片制作一张卡片是很容易的。就此而言，你甚至可以轻松地购买一张预制卡。但是，当你是一个书呆子，并且知道你的另一半会喜欢你自己做的一张书呆子卡片时，会发生什么呢？

我发现自己处于那个位置，于是开始了一场疯狂的追逐，使用 [ggplot2](https://ggplot2.tidyverse.org/) 生成图像，使用 [RMarkdown](https://rmarkdown.rstudio.com/) 开发卡片本身，创建 HTML 和 PDF 格式的卡片。我在这里也了解到了[的](https://here.r-lib.org/)包。旅程来了！

大约一个月前，我妻子问我是否会为她的情人节礼物做些古怪的东西。作为一个尽职尽责的丈夫，我提前两周开始工作。由于生活责任，整个项目花费了 6 到 15 个小时。

当我在北卡罗来纳 A&T 州立大学攻读物理学时，数学系举办了一次数学主题活动，其中包括爱情方程式。这给了我灵感。我决定用图形函数来创建心形，并拼出一条信息。我在 RStudio 中选择了 R，因为那是我目前专攻可视化技能的地方。

傅立叶变换将是我将来要尝试的东西。我还需要弄清楚如何在 ggplot2 中为给定的 x 值显示多个 y 值。我最终会到达那里，但这比我这一轮想要的工作要多。

# 过程

我在这里发现了几个心形方程(是的，我知道它们在解剖学上不正确)。与 Mathematica 不同，我不能在 r 中绘制数学函数。解决方法是插入函数并进行增量计算，以绘制每个点:

```
# Theta values
t <- seq(-pi,pi,0.01)# X Values
x <- 16*0.25*(3*sin(t) - sin(3*t))# Y Values
y <- 13*cos(t) - 5*cos(2*t) - cos(4*t)
```

我对心形使用了极坐标，因为笛卡尔坐标不能正确工作。我想把结果放在原点的中心，但是遇到了时间限制的问题，把它留到将来的迭代中。我用误差线制作了字母 I，用 [ggforce](https://cran.r-project.org/web/packages/ggforce/index.html) 包装上的圆形几何图形制作了字母 O。你可以在这里找到我为每个字母[使用的代码](https://github.com/davidmvermillion/EquationsofLove/blob/main/Code/FirstEquations.R)。

在我制作完图像之后，是时候使用 RMarkdown 了！几个月前我第一次听说 RMarkdown，但这是我第一次使用它。有一个学习曲线，但并不太可怕。HTML 输出是最简单的，但是 [PDF](https://davidmvermillion.com/wp-content/uploads/2021/02/ValentinesDayCardPDF-1.pdf) 输出也是可行的——需要做很多调整。我仍然没有得到我想要的确切结果，但是运用[帕累托法则](https://en.wikipedia.org/wiki/Pareto_principle)，我认为已经足够接近了。我找到并使用 here 包来引用我的文件结构，因为 RMarkdown 与 RStudio 的其他部分(或者至少是在 RStudio 中打开的脚本文件)看待全局环境的方式不同。

# 结论

这个令人兴奋的项目让我为妻子制作了一张书呆子式的情人节卡片，并在这个过程中学到了很多！你可以在这里找到我的来源列表[和我的整个 GitHub 项目](https://github.com/davidmvermillion/EquationsofLove/blob/main/README.md)[这里](https://github.com/davidmvermillion/EquationsofLove)。如果你有任何改进的建议，我很乐意在下面的评论区听到它们！

*原载于 2021 年 2 月 17 日 https://davidmvermillion.com*[](https://davidmvermillion.com/how-to-create-a-dazzlingly-nerdy-valentines-day-card-using-rmarkdown-and-ggplot2-in-rstudio/)**。**