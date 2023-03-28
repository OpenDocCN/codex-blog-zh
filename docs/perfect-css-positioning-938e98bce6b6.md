# 完美的 CSS 定位

> 原文：<https://medium.com/codex/perfect-css-positioning-938e98bce6b6?source=collection_archive---------9----------------------->

## [法典](http://medium.com/codex)

## 了解位置属性和 Z 索引

![](img/1c8e461423fe8230951a3541b1fffbda.png)

来自 [Pexels](https://www.pexels.com/photo/person-in-black-leather-boots-lying-on-brown-cardboard-boxes-4553183/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的 [cottonbro](https://www.pexels.com/@cottonbro?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 摄影

# 为什么排列盒子比看起来更难

一开始，使用基本 CSS 设计应用程序的样式可能感觉相当简单和直接。您可以轻松地更改颜色、大小、字体等。我们的元素位于由边距、边框和填充组成的框中的概念很容易理解…但是如何定义这些框在页面上的位置呢？这是事情变得有点棘手的地方。

我们没有使用其他语言时使用的典型调试工具，比如控制台日志或错误消息。因此，当我们的造型表现不如预期时，很难找出问题所在。然而，熟悉一些默认行为以及如何操作它们可以节省我们大量的时间和挫折。

# 位置属性

所有元素的默认位置是静态的。静态元素遵循 HTML 文档的自然流程(垂直地，一个元素接一个元素)。如果我们给一个元素的 position 属性任何其他的值，比如相对的、绝对的、固定的或者粘性的，它就被认为是一个定位的元素。

只有当我们改变了这个缺省值，我们才能够指定我们想要将元素移动到的上、下、左、右的距离。根据位置属性的值，这些方向指示具有不同的含义。例如，如果一个元素的位置设置为相对，上限值为 10px，那么它会将元素从其先前位置的顶部移动 10px。相对定位相对于其先前的静态位置调整放置。

绝对位置值和固定位置值将元素完全从文档的默认流程中移除。这可能会导致一些奇怪的行为，比如元素堆叠在一起，因为常规文档流中的元素不会注意到绝对的和固定的元素。这两个值的主要区别在于，绝对元素的位置相对于其最近的父元素，而固定元素的位置相对于其包含的块。对于固定元素，此规则有一个例外。如果它的一个祖先具有 transform、perspective 或 filter 属性，则该祖先充当它的包含块，而固定元素相对于它放置。

最后，我们来谈谈粘性定位。需要注意的是，并不是所有的浏览器都支持它，但是如果使用得当，它还是很棒的。当我们将元素的位置定义为 sticky 时，它保持在正常的 HTML 文档流中。但是，它的位置是相对于具有滚动机制(如溢出:隐藏、滚动、自动或覆盖)的最近祖先设置的。另请注意，它仍将停留在其包含块内。这是导航栏中常见的行为，所以当我们向下滚动页面时，它会继续留在视窗中。

# z 索引和堆叠索引

就像地理位置坐标一样，x 和 y 索引的值决定了我们的元素在二维平面上的位置。z 指数决定了我们元素的第三维度位置。这给了我们在其他参考点之上或之下放置东西的能力。默认情况下，所有元素的 z 索引都设置为 0。所以，所有的元素都在同一个平面上，一个接一个地垂直渲染。当我们为 z-index 分配不同的值时，具有最高 z-index 的元素将出现在顶部。

这里有个问题…我们不能只给静态定位的元素添加一个 z 索引。嗯，你可以，但不会有任何效果。所以首先，一定要定义元素的位置是相对的，绝对的，固定的还是粘性的。更改位置属性会创建所谓的堆叠上下文。

正如 MDN Web Docs 中所描述的，“堆叠上下文是 HTML 元素沿着相对于用户的虚拟 z 轴的三维概念化”。

其中一个最好的分析是 Photoshop 图层和群组。如果你熟悉 Photoshop，你会明白你可以把照片、图画、商标等组合在一起。所有这些东西都将被平铺到一个“层”中。然后，您可以添加其他层，并定义您想要的顶部和底部。将每个堆叠上下文想象成由父元素包围的一组子元素可能会有所帮助。它们不会与其他堆叠上下文混合。该组可以放在其他层的上面或下面，但它将始终粘在一起。

包含指定 z 索引的元素的子元素将跟随其父元素的位置。它不会与其父元素之外的元素进行比较。解决这个问题的唯一方法是移除父对象的 z 索引，从而将其从堆栈上下文中一起移除。不要混淆堆叠上下文和 DOM 树中元素的位置。这是不一样的，因为堆栈上下文只关注有 z 索引的非静态元素！

同样重要的是要注意，堆叠上下文可能会受到其他属性的影响，如不透明度、变换、隔离和其他一些属性。如果您看到一些意外的行为，请注意这一点。

isolation 属性特别有用，因为它实际上允许我们稍微打破规则，将我们想要的特定元素添加到堆栈上下文中——包括静态定位的元素！

# 结论

咻！那是很难接受的。如果这些概念需要一段时间来掌握，这是可以理解的。打开一个最近的项目，使用 position 属性。尝试在这里和那里添加 z 索引。你看到变化了吗？如果没有，你能找出原因吗？记住，对你的 CSS 定位真正感到自信的最好方法是实践。祝你好运！

# 资源

[](https://www.joshwcomeau.com/css/the-importance-of-learning-css/) [## 学习 CSS 的重要性

### 我知道很多超级天才的开发者都有一个共同的致命弱点:CSS。这不是试图“逃脱”CSS，而是…

www.joshwcomeau.com](https://www.joshwcomeau.com/css/the-importance-of-learning-css/) [](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context#the_stacking_context) [## 堆叠上下文

### 堆叠上下文是 HTML 元素沿着相对于…的假想 z 轴的三维概念化。

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Positioning/Understanding_z_index/The_stacking_context#the_stacking_context) [](https://www.w3.org/Style/CSS/#specs) [## 级联样式表

### 级联样式表(CSS)是一种向 Web 文档添加样式(例如，字体、颜色、间距)的简单机制…

www.w3.org](https://www.w3.org/Style/CSS/#specs)