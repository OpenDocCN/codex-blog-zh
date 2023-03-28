# OMONP#2:用 LazySizes 进行图像延迟加载

> 原文：<https://medium.com/codex/1-minute-to-become-a-better-developer-20-a6f93db2c180?source=collection_archive---------11----------------------->

## [法典](http://medium.com/codex)

## 一个比 loading="lazy "属性具有更好浏览器支持的库的快速介绍。

欢迎来到问题#1 **O** ne **M** inute，**O**ne**N**PM**P**package，在这里你会发现很酷的 NPM 软件包，以及如何在一分钟内使用它们。

## [⏮️](https://jportella93.medium.com/1-minute-to-become-a-better-developer-14-a2ff85046b8c) [🔛](https://jportella93.medium.com/omonp-one-minute-one-npm-package-bea2c9c3636)⏭

![](img/f1e8ea84161550677cf05672a363549c.png)

[我们将建造什么](https://codepen.io/jportella93/pen/MWJYeLj)

## 问题是

我们网站上的图像同时加载，包括那些不在视图中的图像，阻止了其他资源的下载。

这使得我们的网站需要更长的时间来加载，**对我们的 UX** 产生了不好的影响。

## 一个解决方案

我们将使用一个叫做 [LazySizes](https://github.com/aFarkas/lazysizes) 的漂亮的小软件包，使图像只在滚动到视图中时才加载。

为此，我们只需:

1.  用一个`data-src`属性替换图片上的`src`属性，这样浏览器就不会自动下载它们。此外，这允许 LazySizes 获取图像源，以便在滚动到视图中时请求它。
2.  给图像添加一个`lazyload`类，这样 LazySizes 就知道它必须作用于那个元素。

这里有一个快速演示，滚动以使图像出现。你可以在浏览器的 DevTools 上查看网络标签，当你滚动的时候，你会看到图像请求正在完成！

简单的图像 lazyloading 与 LazySizes。

## 如果你喜欢这个故事，你可能也会喜欢:

[](https://jportella93.medium.com/1-minute-to-become-a-better-developer-14-a2ff85046b8c) [## OMONP#1

### 了解如何在一分钟内制作一张 3D 银行卡。

jportella93.medium.com](https://jportella93.medium.com/1-minute-to-become-a-better-developer-14-a2ff85046b8c) [](/codex/omonp-3-building-a-carousel-with-swiper-36d6d92d159a) [## OMONP#3:用 Swiper 构建一个旋转木马

### 让我们在一分钟内构建一个具有延迟加载、导航和分页的触摸触发的图像滑块。

medium.com](/codex/omonp-3-building-a-carousel-with-swiper-36d6d92d159a) 

## [⏮️](https://jportella93.medium.com/1-minute-to-become-a-better-developer-14-a2ff85046b8c) [🔛](https://jportella93.medium.com/omonp-one-minute-one-npm-package-bea2c9c3636) [⏭](/codex/omonp-3-building-a-carousel-with-swiper-36d6d92d159a)