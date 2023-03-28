# Android 本地化:Transifex Native 的高级指南

> 原文：<https://medium.com/codex/android-localization-an-advanced-guide-for-transifex-native-f1471bdadc67?source=collection_archive---------11----------------------->

![](img/6491f482bb46b60576599d9425e0929f.png)

Android 是全球领先的移动操作系统。根据 Statista 的数据，Android 拥有超过 70%的全球市场份额。这就是 Android 本地化变得如此重要的原因。用户倾向于选择文本、数字、日期、货币等本地化的应用。

[Android Studio](https://developer.android.com/studio) 为开发者提供了很多处理 Android 本地化的工具，只要遵循一些简单的原则。字符串应该放在字符串资源文件中，如“strings.xml”。应该避免将硬编码的字符串放在布局文件或代码中。如果你试图这样做，Android Studio 甚至会警告你。本地化字符串需要创建单独的“strings.xml”文件，并将它们放在由各自的语言环境命名的子目录中。该文件可以手动编辑，也可以通过 Android Studio 的翻译编辑器编辑。

这些“strings.xml”文件可以复制、翻译并移回到项目中。尽管这看起来相当简单，但是当团队中的多个开发人员在“手动”订购和管理翻译的同时添加或删除新字符串时，复杂性会显著增加。这种方法的另一个问题是，更新现有翻译或添加对新语言环境的支持需要发布新的应用程序更新。

这篇文章将解释在你的应用中集成 [Transifex Native](https://www.transifex.com/native/) 将如何改善你的 Android 本地化工作流程。

目录:

*   [空中翻译传送](https://www.transifex.com/blog/#Over-the-air_Translation_Delivery)
*   [将源字符串推送到 Transifex 并从 transi fex 中拉出翻译](https://www.transifex.com/blog/#Pushing_Source_Strings_to_and_Pulling_Translations_from_Transifex)
*   [推送源字符串](https://www.transifex.com/blog/#Pushing_Source_Strings)
*   [拉动平移](https://www.transifex.com/blog/#Pulling_Translations)
*   [在 Transifex UI 中处理复数](https://www.transifex.com/blog/#Handling_Pluralization_in_the_Transifex_UI)

# 空中翻译传送

使用空中翻译交付，团队可以更新翻译后的字符串，而无需发布新的应用程序更新。

当使用 Transifex Native SDK 时，您也可以在发布更新之前将最新的翻译捆绑到应用程序中。

由您来控制应用程序何时使用异步 [fetchTranslations](https://transifex.github.io/transifex-java/com/transifex/txnative/TxNative.html#fetchTranslations-java.lang.String-) 方法从 Transifex 获取最新的翻译。当 SDK 下载了最新的翻译后，它们将在下次应用程序启动时可用。

让我们从头到尾看看这些看似合理的场景:

*   “Awesome”应用程序的开发人员通过 transifex.jar 命令行工具将字符串从 Android Studio 推送到 Transifex。
*   本地化团队翻译推送的字符串。
*   开发人员使用 transifex.jar 的 pull 命令，下载这些翻译并存储在“txstrings.json”文件中，然后这些文件被捆绑在应用程序中。SDK 将尝试从这些文件中加载存储的翻译。
*   开发团队在应用程序启动时添加了对 [fetchTranslations](https://transifex.github.io/transifex-java/com/transifex/txnative/TxNative.html#fetchTranslations-java.lang.String-) 方法的调用，这样应用程序就可以更新缓存的翻译。
*   该团队在 Google Play 或另一家商店发布了新版本。发布后不久，支持团队就发现了一些缺失或不正确的翻译。
*   团队无需重新捆绑这些翻译并发布新的更新，只需从 Transifex web 界面添加缺失的翻译并修复不正确的翻译即可。该应用程序将自动下载和更新上述翻译，并在下次应用程序启动时提供服务！

# 将源字符串推送到 Transifex 并从 transi fex 中拉出翻译

Transifex 原生 SDK 附带了一个命令行工具，允许 Android 开发人员直接与 [Transifex CDS](https://www.transifex.com/blog/2020/cds/) 进行交互，而无需处理多个“strings.xml”文件。

# 推送源字符串

使用命令行工具的 push 命令，可以让工具解析项目的源字符串，并将它们发送到 Transifex。

首先，您可以按照[资源库](https://github.com/transifex/transifex-java#transifex-command-line-tool)上的说明构建或下载 transifex.jar。然后，您可以从命令行输入项目的根目录，并执行与下面类似的命令:

```
java -jar /path/to/transifex.jar push -t <transifex_token> -s <transifex_secret> -m <app_module_name>
```

复制

push 命令行为可以通过许多不同的参数来控制。您可以在这里或通过执行以下命令来了解关于参数[的更多信息:](https://github.com/transifex/transifex-java#pushing)

```
java -jar /path/to/transifex.jar help push
```

复制

# 拉翻译

如上所述，您还可以使用命令行工具从 Transifex 下载(或提取)翻译到您的 Android Studio 项目中。拉命令如下所示:

```
java -jar /path/to/transifex.jar pull -t <transifex_token> -m <app_module_name> -l <locale>
```

复制

要获得 pull 命令的完整参数列表，可以执行以下命令:

```
java -jar /path/to/transifex.jar help pull
```

复制

pull 命令需要一个将从 Transifex 下载的已翻译语言环境列表和您的应用程序的模块名称。该工具将下载的翻译放入您的模块的“资产”文件夹中，其内容会自动捆绑在应用程序中，并且可以在运行时由 Transifex SDK 访问。

# 在 Transifex UI 中处理多元化

Android 的 Transifex 原生 SDK 也支持开箱即用的多元化。命令行工具将解析在源字符串中找到的任何“复数”,并将它们推送到 Transifex。同时，SDK 将在 Android 应用程序中正确呈现翻译后的复数字符串。

该功能最大限度地降低了由于每个地区不同的多元化规则而导致的不正确翻译的风险，因为团队将在用户友好的 Transifex web 界面中直接处理所有的多元化翻译。

*以上 Android 本地化指南最初发表在* [*Transifex 博客上。*](https://www.transifex.com/blog/2021/android-localization-an-advanced-guide-for-transifex-native/)