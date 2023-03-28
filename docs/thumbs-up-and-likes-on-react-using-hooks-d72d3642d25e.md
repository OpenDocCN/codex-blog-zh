# 竖起大拇指和喜欢用钩子反应

> 原文：<https://medium.com/codex/thumbs-up-and-likes-on-react-using-hooks-d72d3642d25e?source=collection_archive---------2----------------------->

![](img/3c207eca8541aa18a9cd9591a1a92b32.png)

在今天这个时代，喜欢或任何反应按钮已经成为一个互联网标准。无论我们是在推特上表达我们的想法，在 LinkedIn 上宣布我们升职了，还是在 Instagram 上发布我们在餐厅的餐盘，人们都会不由自主地点击那个喜欢按钮，就像这是他们一生中看到的最后一个帖子一样。这就像一种新的原始本能。所以很自然地，训练营的编码挑战会是，像按钮或竖起大拇指或任何我们想做的事情。了解我们如何在项目中放置一个按钮，并在每次点击时增加计数。

还记得我们如何给 YouTube 视频竖起大拇指或竖起大拇指吗？这就是我们在这个练习中要做的。我们会对播客表示赞同或反对。这是我练习编码挑战题时的选择。尽管可能会变得很苛刻，但我喜欢竖起大拇指的同时竖起大拇指的想法。很诚实，很有人情味。

![](img/916a8626df93f56ac7542212d474aaeb.png)

凯文知道他的拇指朝下。

对于我的 React 项目，我使用的 CSS 库是 [Material-ui](https://mui.com/) 。我喜欢它，因为它非常简单。只需安装、导入您想要使用的工具，就像您在 React 中导入其他所有东西一样，并使用他们易于理解的文档在您的 web 应用程序中创建一切，就像您可视化它一样。

要导入拇指图标，转到[材质-用户界面图标](https://mui.com/components/material-icons/)页面并选择它们。转到您想要添加按钮的 React 文件并导入它们。

```
import ThumbUpIcon from ‘@material-ui/icons/ThumbUp’;import ThumbDownIcon from ‘@material-ui/icons/ThumbDown’;import Button from ‘@material-ui/core/Button’;
```

一旦我们从 Material-ui 导入了按钮和图标，让我们使用[钩子](https://reactjs.org/docs/hooks-intro.html)来增加每次点击图标的次数。我们从从`redux`导入`useState`开始。

```
import React, { useState } from ‘react’;
```

既然我们已经导入了`useState`，让我们继续在函数组件中声明与计数相关的钩子。

```
const [countUp, setCountUp] = useState(0)const [countDown, setCountDown] = useState(0)
```

最后一步是呈现这些图标，我们将在代码行中调用`onClick`事件处理程序。

```
<Button onClick={() => setCountUp(countUp + 1)}><ThumbUpIcon />{`${countUp === 0 ? ‘ ‘ : countUp}`}</Button><Button onClick={() => setCountDown(countDown + 1)}><ThumbDownIcon />{`${countDown === 0 ? ‘ ‘ : countDown}`}</Button>
```

现在，让我们一次点击一次来表达我们对这个播客的看法。

![](img/d6ed18a549831fee9376b5288b2b3aab.png)

从我的项目实际截图。对了，大声喊出来黑女孩书呆子播客。

当然，我们需要在后端数据库表中添加喜欢和不喜欢的东西(或者您想调用的方式)。但是这将是在 React 项目中实现反应点击的简单代码。很快，你将部署你的网站，用户会像西斯科和埃伯特一样竖起大拇指(或竖起大拇指)。

![](img/6d020b6e69902546da1fd541c49d1748.png)

**概要:**

1.  使用材质 ui 库实现按钮和图标。
2.  使用 React 钩子来处理事件和增加数字。