# 你必须知道的 Android 通用编程规则

> 原文：<https://medium.com/codex/common-progaurd-rules-you-must-know-for-android-189205301453?source=collection_archive---------3----------------------->

![](img/c70110588a7dcc3f8597f3f92d69991e.png)

您可以用来保护/优化/测试/调试/修复您的应用的`ProGaurd`选项列表！

对于那些不熟悉`ProGaurd`的人来说， [*ProGuard 是一个开源的 Java 类文件收缩器、优化器、混淆器和预校验器。因此，ProGuard 处理的应用程序和库更小、更快，并且在一定程度上增强了对逆向工程的抵抗力。*](https://www.guardsquare.com/manual/home)

```
// app/build.gradleandroid {
    buildTypes {
        release {
            // Enables code shrinking, obfuscation, and optimization for only
            // your project's release build type.
            minifyEnabled true

            // Enables resource shrinking, which is performed by the
            // Android Gradle plugin.
            shrinkResources true

            // Includes the default ProGuard rules files that are packaged with
            // the Android Gradle plugin. To learn more, go to the section about
            // [R8 configuration files](https://developer.android.com/studio/build/shrink-code#configuration-files).
            proguardFiles getDefaultProguardFile(
                    'proguard-android-optimize.txt'),
                    'proguard-rules.pro'
        }
    }
    ...
}
```

让我们把这个故事分成两部分:

# 调试程序/R8

如果你遵循 android 规范，并且根本不使用 java 的反射 API，那么在大多数情况下，你应该可以使用默认的 ProGaurd/R8 应用程序。但是如果没有，您可以使用这些选项来帮助调试:

`-dontshrink`

暂停*缩码。*默认情况下，ProGuard/R8 压缩代码:它删除所有未使用的类和类成员。

`-dontoptimize`

暂停*代码优化*。默认情况下，ProGuard/R8 优化所有代码。它内联并合并类和类成员，并在字节码级别优化所有方法。

`-dontobfuscate`

最重要的是，这个选项暂停了*代码混淆*。默认情况下，ProGuard/R8 对代码进行模糊处理:它给类和类成员分配新的短随机名称。它移除只对调试有用的内部属性，如源文件名、变量名和行号。

`-printseeds <file name>`

指定详尽地列出与我们接下来将看到的各种`-keep`选项匹配的类和类成员。

# 保留不安全的物品

现在，在探究了到底是什么导致你的代码在*缩减*后停止工作之后，你可以指示 R8 在*缩减期间必须`keep`做什么。与我们上面看到的调试选项不同，你还可以在 Java 类中用`@androidx.annotation.Keep`注释来指示 R8，例如:*

```
@Keep
enum class EmailRegisterFlow {
    *REGISTRATION*, *BANK_TRANSACTION* }
```

虽然添加一个注释来排除`R8` *缩小*似乎很容易，但这也意味着你可能会在最终发布版本中堆积一些不需要的代码，因为`@Keep`注释会一次性排除类的收缩、混淆和优化！为了让它以老的方式工作(这是更加可定制和有效的)，即在`ProGaurd`文件中添加规则或选项，你可以使用这些:

`-keep`[，*修饰符*](https://www.guardsquare.com/manual/configuration/usage#keepoptionmodifiers) ，...][*class _ specification*](https://www.guardsquare.com/manual/configuration/usage#classspecification)

就像 Java 的`@Keep`注释一样，保留了指定的类。该选项主要用于指定入口点(类似于`main`函数),但也用于保留仅使用 Java 的反射 API 的类，因此在*树抖动期间无法访问这些类。*

例如

```
-keep enum com.example.bean.**
```

`-keepclassmembers`[，*修改器*](https://www.guardsquare.com/manual/configuration/usage#keepoptionmodifiers) ，...】 [*类 _ 规格*](https://www.guardsquare.com/manual/configuration/usage#classspecification)

当您只想在对类应用树抖动后保留类成员时，请使用此选项。注意这里的区别，`keepclassmembers`将只应用于保留类的成员(不管是否有对它的引用，就像`keep`)如果类本身在树抖动期间被保留的话。

例如

```
-keepclassmembers enum * {
    *<fields>*;
}
# This will retain all the enums fields for all the retained enum classes.
```

`-keepnames`[*class _ specification*](https://www.guardsquare.com/manual/configuration/usage#classspecification)

这种情况下，在应用了树抖动之后或者在代码混淆期间，您只想保留类和类成员的名称。如果你也使用反射 API 引用一个类或它的成员(例如，使用`Class.forName(“Foobar”).newInstance()`初始化一个类)，就可以使用这个方法。就像`keepclassmembers`一样，`keepnames`只在类本身在树抖动期间被保留的情况下才被应用(意思是“Foobar”或者它的成员在上面的例子中直到`R8`找到对它的引用或者使用`keep`选项被保存才会被保存)。

例如

```
-keepnames class com.example.bean.**
```

这就是适合几乎所有情况的基本 R8/普罗高德选项(至少对我来说)。对于更高级的定制，您可以查看下面这个故事的来源。

 [## ProGuard 手册:用法| Guardsquare

### 本页列出了 ProGuard 的所有可用选项，按逻辑分组。递归读取配置选项从…

www.guardsquare.com](https://www.guardsquare.com/manual/configuration/usage) 

[https://developer.android.com/studio/build/shrink-code](https://developer.android.com/studio/build/shrink-code)

[](https://www.preemptive.com/2019/04/r8-a-step-in-googles-android-build-performance-roadmap) [## R8:谷歌 Android 构建性能路线图的一步

### 谷歌最近推出了 R8，这是一个新工具，旨在取代 ProGuard 成为 Android 版本中的默认收缩器…

www.preemptive.com](https://www.preemptive.com/2019/04/r8-a-step-in-googles-android-build-performance-roadmap)