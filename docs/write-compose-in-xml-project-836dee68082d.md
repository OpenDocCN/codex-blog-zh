# 在 XML 项目中编写合成

> 原文：<https://medium.com/codex/write-compose-in-xml-project-836dee68082d?source=collection_archive---------6----------------------->

## XML-Jetpack 组合互操作性

![](img/24b6f5683313f8c58c8fb5b19c3c80fa.png)

[信号源](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.raywenderlich.com%2F7032631-jetpack-compose-tutorial-for-android-getting-started&psig=AOvVaw1Ig_ZKQ2dHF5vmisKqg4EQ&ust=1653902500501000&source=images&cd=vfe&ved=0CA4Q3YkBahcKEwiYw76LsYT4AhUAAAAAHQAAAAAQLQ)

您有一个 XML 项目，并且希望包含可组合的函数来呈现给 UI。你打算怎么做？Google 已经引入了 Compose 和 XML 之间的互操作性，以便于移植。

# 步骤 0 —配置梯度

在您的顶层`build.gradle`文件中，添加 compose_version 扩展属性:

```
buildscript {
  ext {
    compose_version = '1.0.5'
  }
  ...
}
```

在您的模块级`build.gradle`文件中，添加以下几行:

```
android {
  ...
  buildFeatures {
    compose true
  }
  composeOptions {
    kotlinCompilerExtensionVersion compose_version
    kotlinCompilerVersion '1.5.10'
  }
}dependencies {
  ...
  implementation "androidx.compose.ui:ui:$compose_version"
  implementation "androidx.compose.material:material:$compose_version"
  implementation "androidx.compose.ui:ui-tooling-preview:$compose_version"
  androidImplementation "androidx.compose.ui:ui-test-junit4:$compose_version"
}
```

**注意**:`kotlinCompilerVersion`必须匹配项目`build.gradle`类路径中的 Gradle 插件。

# 步骤 1 —在 XML 中添加占位符

为了在 XML 项目中使用 Jetpack Compose，您需要将一个`ComposeView`添加到 XML 文件中。

```
<androidx.compose.ui.platform.ComposeView
  android:id="@+id/compose_view"
  android:layout_width="match_parent"
  android:layout_height="match_parent" />
```

# 步骤 2 —编写您的合成代码

一旦我们在 XML 文件中有了视图，我们就可以用它来编写组件了。

# 结论

如果您有一个想要迁移到 Compose 的现有项目，使用这个互操作性特性可以很容易地完成。不要试图一次迁移整个项目，而是逐个组件或逐个屏幕地迁移。

目前，谷歌地图或 ExoPlayer 等各种高级视图都没有可用的组件。如果您的应用程序需要这些视图，明智的做法是使用包含可组合部分的 XML 项目。

谢谢你看我的文章！✌️

```
**Want to connect?**My [GitHub](https://github.com/cybercoder-naj) Profile
My [Portfolio](https://cybercoder-naj.github.io) Website
```