# Android:优化包选项

> 原文：<https://medium.com/codex/android-exclude-file-from-f63d031e2f2d?source=collection_archive---------0----------------------->

![](img/b72441d62505988e0ce88c560ee6405d.png)

```
@Incubating interface PackagingOptions
```

用于配置 APK 打包选项的 DSL 对象。

打包选项配置有三组路径:首选合并和排除:

与首选模式匹配的首选路径将被选入 APK。如果有多条路径与第一个选择相匹配，将只选择第一个找到的路径。合并路径…