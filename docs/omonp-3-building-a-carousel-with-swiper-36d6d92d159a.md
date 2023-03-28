# OMONP#3:用 Swiper 构建一个旋转木马

> 原文：<https://medium.com/codex/omonp-3-building-a-carousel-with-swiper-36d6d92d159a?source=collection_archive---------6----------------------->

![](img/1a574144146f01746d5701cc24049845.png)

照片由[替代代码](https://unsplash.com/@altumcode?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

## 让我们在一分钟内构建一个具有延迟加载、导航和分页的触摸触发的图像滑块。

欢迎来到第 3 期 **O** ne **M** inute，**O**ne**N**PM**P**package，在这里你会发现很酷的 NPM 软件包，以及如何在一分钟内使用它们。

## [⏮️](/codex/1-minute-to-become-a-better-developer-20-a6f93db2c180) [🔛](https://jportella93.medium.com/omonp-one-minute-one-npm-package-bea2c9c3636)

![](img/e366717bccb77c532ba71d81b09e6e2b.png)

[我们将建造什么](https://codepen.io/jportella93/pen/PoWjaaG)

## 问题是

我们想实现经典的图像轮播目前遍布互联网。

虽然这看起来是一个很容易实现的任务，但是有一些小的特性和边缘情况实际上使得这个任务不那么简单，比如图像延迟加载、循环和分页。

## 一个解决方案

我们将使用一个叫做 [Swiper](https://www.npmjs.com/package/swiper) 的漂亮的小软件包来快速实现我们的 carousel。

## 1.导入 Swiper 的 JavaScript 和 CSS

## 2.添加 Swiper 的布局，其中基本元素应该带有特定的类名

*   `swiper-container`是主滑块容器
*   `swiper-wrapper`是额外需要的包装器
*   `swiper-slide`是每张幻灯片的包装
*   可选的`swiper-pagination`是底部的一排点，每个点代表一张幻灯片，可以触发导航到特定的幻灯片。此外，它们对当前活动的幻灯片有不同的样式
*   可选的`swiper-button-prev`和`swiper-button-next`是滑块两侧的箭头

此外，对于延迟加载:

*   给`swiper-slide`中的图片添加一个`swiper-lazy`类
*   添加一个占位符`swiper-lazy-preloader`来显示图像是否尚未加载
*   图像属性`src`应该替换为`data-src`以避免默认加载行为

## 3.在我们的脚本文件中初始化 Swiper

*   `loop`使最后一张幻灯片回到第一张幻灯片，反之亦然
*   `lazy.loadPrevNext`预加载下一幅图像，以避免显示加载占位符(如果可能)
*   `pagination`和`navigation`设置这些元素的配置

## 4.把所有的放在一起

这是 CodePen 上的一个快速演示。你可以在浏览器的 DevTools 上查看网络选项卡，当你滑动时，你会看到按需完成的图像请求！

具有导航和分页功能的延迟加载图像浏览器

## 如果你喜欢这个故事，你可能也会喜欢

[](https://jportella93.medium.com/1-minute-to-become-a-better-developer-14-a2ff85046b8c) [## OMONP#1

### 了解如何在一分钟内制作一张 3D 银行卡。

jportella93.medium.com](https://jportella93.medium.com/1-minute-to-become-a-better-developer-14-a2ff85046b8c) [](/codex/1-minute-to-become-a-better-developer-20-a6f93db2c180) [## OMONP#2

### 学习如何在一分钟内惰性加载图像。

medium.com](/codex/1-minute-to-become-a-better-developer-20-a6f93db2c180) 

## [⏮️](/codex/1-minute-to-become-a-better-developer-20-a6f93db2c180) [🔛](https://jportella93.medium.com/omonp-one-minute-one-npm-package-bea2c9c3636)