# 优雅 CSS 初学者指南

> 原文：<https://medium.com/codex/beginners-guide-to-elegant-css-217a6a31ed7a?source=collection_archive---------2----------------------->

![](img/393c0bb4e02e14964b0d58010e8cb33a.png)

杰斯·贝利在 [Unsplash](https://unsplash.com/s/photos/design?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的照片

网页设计的一个基本目标是将内容(“什么”)和设计(“如何”)分开。从用户的角度来看，我们希望在不同的设备上有一致的体验。从提供商的角度来看，我们希望能够用新的设计来重塑品牌，而不必重新制作数千页的旧内容。

当前 CSS 框架的激增违反了这一原则，因为它要求大量使用嵌入在 HTML 内容中的特定于框架的 CSS 类。

HTML5 向实现“是什么/如何”的分离迈出了一大步，新标签传达了一大块内容(文章、页眉、页脚、旁白、主页面、标记、导航、细节、摘要)的语义/结构**目的**，而不是其设计。

这篇文章的目的是提出一套简单的规则，更智能地使用 HTML5 和 CSS 来实现更好的什么/如何分离。

**规则 1:善用 HTML5 标签**。作为一个作者，你对你的内容有一个传播意图，所以使用与你的意图相匹配的标签。这里是 [**好导**](https://www.semrush.com/blog/semantic-html5-guide) 。

规则 2:样式标签除非绝对必要，否则不要使用类或 id——我怀疑这是否真的有必要。这促进了一致的设计。你真的需要不止一种方法来设计 h1 >或 p >发型吗？如果是这样的话，很可能是因为它在其他东西里面，比如一个<节>或一个<页脚>，你可以在 CSS 中把它分配给<页脚> > < h1 >对< main > > < h1 >。我想知道如果 HTML5 和 CSS3 简单地消除了 DIV、CLASS 和 ID 会发生什么？(嗯，javascript 会更难使用——见下一条！)

**规则 3:使用 CSS** 而不是 javascript 执行“普通”用户交互(UI)和转换。这有时很棘手，但是有人可能已经解决了这个问题，你可以改编他们的例子。周六我会把我最喜欢的放在一篇文章里。希望你能在评论中贡献你自己的想法，我会感谢你的！

规则 4:除非绝对必要，否则避免使用 javascript。如果您正在编写一个游戏，或者生成一个动态图表，或者格式化一个 ajax 获取的 json 数据的传入表，那么就去做吧——看看您是否可以用普通的 javascript 而不是会降低您的加载速度的包来完成这些工作。

规则 5:挑选一些有意义的颜色并用 CSS 变量定义它们，这样如果你决定你的高亮颜色应该是紫色而不是黑色，你只需要改变一次。

**规则 6:按照逻辑层次写你的 CSS 文件**，前面有一些定义页面整体设计的设置。(我希望你只需要一个！请命名为 style.css！)下面是前几行的一个很好的例子。

```
/* the basics */
:root { --primary:darkgreen; --light:mediumseagreen;}
*,*::before,*::after {box-sizing: border-box;}
body { margin: 0; padding: 0; 
  font-size: 17px; 
  font-family: sans-serif;}
main, section, footer, aside {padding: 1em;}
main > h1 {font-weight:normal; font-size:2.4em;}
/* a bit fancier for icons in your title line */
main > h1 > span {color:var(--light); text-decoration:none;}
/* Style striped tables */
table, td {border:1px solid var(--primary); 
  border-collapse:collapse;}
tr:nth-child(even) {background-color: #light;}
td, th {overflow:visibe; padding: 0.5em 1em;}
th {background-color:var(--primary); color:white;}
```

**规则 8:使用类似 Lighthouse** 的工具来确保你遵循最佳实践。

我怀疑自己能否战胜正在进行的这场相当混乱的辩论。我相信我们都可以通过更好的实践和更好的标准为网络的发展做出贡献。