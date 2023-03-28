# 为什么使用 Git 存储库作为 Flutter 依赖项

> 原文：<https://medium.com/codex/why-use-git-repositories-as-flutter-dependencies-ba979b73b0c3?source=collection_archive---------13----------------------->

![](img/ce894a4e9c387921e25982435e166e3f.png)

由[加布里埃尔·海因策](https://unsplash.com/@6heinz3r?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

所以我最近在 Reddit 上看到这个帖子，要求将您的依赖关系粘贴到`pubspec.yaml`文件中。所以我这样做了，然后我意识到一些事情:我有很多来自 git 仓库的依赖。我也从来没有真正谈过我为什么这么做。所以我想我现在应该谈谈了。

## 怎么做