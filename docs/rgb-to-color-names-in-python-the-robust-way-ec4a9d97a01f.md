# Python 中 RGB 到颜色名称的健壮方法

> 原文：<https://medium.com/codex/rgb-to-color-names-in-python-the-robust-way-ec4a9d97a01f?source=collection_archive---------0----------------------->

## 一个简单的函数，将 RGB 值转换成各种组合的颜色名称。

![](img/9571789679cf1a5eccec19fc5f5355b6.png)

在 [Unsplash](https://unsplash.com/@rohitraj_28?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Rohit Raj](https://unsplash.com/@rohitraj_28?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

这是一个相当简单的问题，有一个名为 [webcolors](https://pypi.org/project/webcolors/1.3/) 的 Python 库可以解决这个问题。我们可以使用 *rgb_to_name* 函数，该函数将 rgb 值作为参数，并返回它们所代表的颜色的名称。让我们来看看它的实际应用:

```
from webcolors import rgb_to_name
named_color = rgb_to_name((255,0,0), spec='css3')print(named_color)
```

输出:

```
red
```

这很好。但是让我们看看它是否能处理一些 RGB 值的随机组合。我们试试(190，53，25)。

```
named_color = rgb_to_name((190,53,25), spec='css3')print(named_color)
```

上面的代码抛出一个错误:

```
ValueError: '#960000' has no defined color name in css3.
```

它告诉我们，我们给出的 RGB 值的组合没有映射到 css3 中任何可用的十六进制代码。换句话说，当函数将十进制值转换为 CSS 十六进制代码时，它不会找到与之关联的颜色名称的精确匹配。让我们来看看一些 RGB 值及其十六进制代码和颜色名称，以便弄清楚:

*   **RGB** →(0.255.0)，**十六进制代码** →#00FF00，**颜色名称**→石灰
*   **RGB** →(178，34，34)，**十六进制代码** →#B22222，**颜色名称**→耐火砖

如果你正在做一个依赖于颜色准确性的项目，这实际上是有意义的。然而，如果您可以凑合使用给定 RGB 值的最接近的颜色名称，我们可以使用找到最接近匹配的东西。

# 解决方案:

我们将使用一种叫做 KDTree 的数据结构。它将为我们给定的 RGB 查找最匹配的十六进制代码，该代码具有与之相关联的颜色名称。你可以在这里阅读更多关于 KDTree [的信息。](https://docs.scipy.org/doc/scipy/reference/generated/scipy.spatial.KDTree.html)

代码:

```
from scipy.spatial import KDTree
from webcolors import (
    css3_hex_to_names,
    hex_to_rgb,
)def convert_rgb_to_names(rgb_tuple):

    # a dictionary of all the hex and their respective names in css3
    css3_db = css3_hex_to_names
    names = []
    rgb_values = [] for color_hex, color_name in css3_db.items():
        names.append(color_name)
        rgb_values.append(hex_to_rgb(color_hex))

    kdt_db = KDTree(rgb_values) distance, index = kdt_db.query(rgb_tuple)
    return f'closest match: {names[index]}'
```

让我们用这个函数得到 RGB 的名称(190，53，25)。

```
print(convert_rgb_to_names((190,53,25)))
```

输出:

```
closest match: firebrick
```

# **结论**

我们可以看到 css3 中*耐火砖*的 RGB 与我们使用的相差不远。如果您需要每个可能的 RGB 组合的颜色名称，可以使用此代码。我希望这篇文章是有帮助的。